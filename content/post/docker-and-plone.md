---
title: "Docker and Plone"
subtitle: "Spinning up Plone on Docker"
date: 2014-01-16T12:48:00+10:00
categories:
  - Development
  - Python
tags:
  - Python
  - Docker
  - Plone
draft: false
---

![an open source project to pack, ship and run any application as a lightweight container](/images/docker.png)

Come across [Docker](http://docker.io/) yet? If not you should check it out!

Summary:

- Portable containers across Development, Staging and Production
  environment(s).
- Guarenteed consistency.
- Lightweight viraulization.
- Excellent Tooling and Ecysystem.
- Growing at an ever expanding rate!

Here's a quick demo of how to spin up a [Plone](http://plone.org/)
instnace using [Docker](http://docker.io/). (*[Plone](http://plone.org/)
is a [Python](http://www.python.org/) powered [Content Management
System](http://en.wikipedia.org/wiki/Content_management_system)*)

```#!bash
ubuntu@vz1:~$ CID=$(docker run -i -t -d -p 80:80 turnkeylinux/plone-13.0:latest)^C
ubuntu@vz1:~$ CIP=$(docker inspect -format='{{.NetworkSettings.IPAddress}}' $CID)^C
ubuntu@vz1:~$ docker logs $CID | egrep "root"^C
ubuntu@vz1:~$ ssh root@$CIP
```

At this point you were presented with a a couple of dialog boxes to
update the system which is typical of every
[TurnKeyLinux](http://www.turnkeylinux.org/) appliance.

This took all but 2 miinutes to download and deploy!

[Docker](http://docker.io/) rocks!

Here are some other interesting links:

- http://shipyard-project.com/
- https://github.com/crosbymichael/skydock
- https://github.com/hashicorp/serf
- https://github.com/coreos/etcd
- https://coreos.com/
- https://github.com/steeve/boot2docker
- https://flynn.io/
- http://ctl-c.io/
- http://blog.ctl-c.io/?p=41

Enjoy! :)
