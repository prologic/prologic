---
title: "A Compose for Docker Machines"
subtitle: "Compose for Docker Machines; declaring your machine configurations."
date: 2015-07-03T01:54:00+10:00
categories:
  - Development
  - Infrastructure
  - Python
tags:
  - Python
  - Docker
  - Compose
  - Machine
  - docker-machine
  - docker-compose
  - factory
draft: false
---

![an open source project to pack, ship and run any application as a lightweight container](/images/docker.png)

Introducing a new tool [factory](https://github.com/prologic/factory)
for composing [Docker](https://docker.com/) machines wrapping around
[docker-machine](https://github.com/docker/machine) itself.

This lets you define a `factory.yml` file like this:

```#!yaml
machines:
  test:
    driver: digitalocean
```

And run:

```#!bash
$ factory up
```

That's it! You can define as many machines as you like and the
configuration options match those of
[docker-machine](https://github.com/docker/machine).

There are also a few other nifty commands:

-  `stop` -- Stop a machine or all machines
-  `rm`   -- Remove a machine or all machines
-  `ls`   -- List all machines

Check it out on [Github](https://github.com/) at:
<https://github.com/prologic/factory>

Enjoy! :)

----

Do check out my other related project
[autodock](https://github.com/prologic/autodock) and
[autodock-paas](https://github.com/prologic/autodock-paas).
