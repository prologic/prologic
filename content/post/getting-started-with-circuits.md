---
title: "Getting started with circuits"
subtitle: "Getting started quickly with circuits. An ASCIInema tutorial!"
date: 2014-03-27T14:23:00+10:00
categories:
  - Tutorial
  - Circuits
  - Python
tags:
  - Python
  - Circuits
  - Tutorial
  - asciinema
draft: false
---

Here's a quick [ASCIInema](http://asciinema.org) tutorial I put
together to show you how to get started with the
[circuits](http://circuitsframework.com) Asynchronous Event-Driven
Application Framework for [Python](http://python.org).

- [Getting Started with circuits](https://asciinema.org/a/8461)

The code in this tutorial is as follows:

```#!python
from circuits import Component, Event

class hello(Event):
    "hello Event"""


class App(Component):

    def hello(self, name="World!"):
        print "Hello {0:s}".format(name)
        return "Hello {0:s}".format(name)
```

Enjoy!

Come join us on `#circuits` on the [FreeNode IRC Network](http://freenode.net)
