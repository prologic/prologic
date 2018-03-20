---
title: "Worker Pools with Circuits"
date: 2011-02-26T15:33:00+10:00
categories:
  - Development
  - Python
tags:
  - Pool
  - Process
  - Worker
  - circuits
  - Python
draft: false
---

For some time now I've been wanting to greatly improve the concurrency support
in [circuits](https://bitbucket.org/prologic/circuits) and implement
**worker pools** (*using threads and/or processes*). A few weeks ago I
finally did that. I thought it would be hard at first until it hit me;

> circuits has all the tools necessary

It turns out implementing "worker pools" in circuits is quite simple.
All you need to do is construct a component that will start a number of
other components as workers (*either in thread mode or process mode*).

This component (*which I call a Pool*) is responsible for managing the tasks
sent to it and utilizing the workers it is managing.

So here's the code (*available since circuits-1.4*):

```#!python
from time import time
from uuid import uuid4 as uuid
from random import seed, choice

from circuits.core.workers import Task, Worker
from circuits.core import handler, BaseComponent

seed(time())

class Pool(BaseComponent):

    channel = "pool"

    def __init__(self, min=5, max=10, process=False, channel=channel):
        super(Pool, self).__init__(channel=channel)

        self._workers = []

        for i in range(min):
            self._workers.append(Worker(process=process, channel=str(uuid())))

    @handler("task")
    def _on_task(self, f, *args, **kwargs):
        workers = float(len(self._workers))
        tasks = [float(len(worker)) for worker in self._workers]
        total = sum(tasks)
        _avg = total / workers

        assigned = None

        for worker in self._workers:
            if len(worker) < _avg:
                assigned = worker.channel
                return worker.push(Task(f, *args, **kwargs), target=worker)

        if not assigned:
            worker = choice(self._workers)
            assigned = worker.channel
            return worker.push(Task(f, *args, **kwargs), target=worker)
```

That's it!

And here's the code for a `Worker`:

```#!python
from circuits.core import handler, BaseComponent, Event

class Task(Event):
    """Task Event"""

class Worker(BaseComponent):

    channel = "worker"

    def __init__(self, process=False, channel=channel):
        super(Worker, self).__init__(channel=channel)

        if process:
            self.start(link=self, process=True)
            self.start()
        else:
            self.start()

    @handler("task")
    def _on_task(self, f, *args, **kwargs):
        return f(*args, **kwargs)
```

Oh and here's a simmple example of how you would use this:

```#!python
from circuits import Task, Pool

def f():
    x = 0
    i = 0
    while i < 1000000:
        x += 1
        i += 1
    return x

p = Pool()
p.start()

# Asynchronous Task
x = p.push(Task(f))

# After a while...

print x

p.stop()
```
