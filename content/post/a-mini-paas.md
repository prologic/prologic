---
title: "A Docker-based mini-PaaS"
subtitle: "A Docker-based mini-PaaS written in Python"
date: 2015-03-24T13:05:00+10:00
modified: 2015-07-03T07:20:00+10:00
categories:
  - Development
  - Infrastructure
  - Automation
  - Python
tags:
  - Python
  - Docker
  - circuits
  - hipache
  - autodock
draft: false
---


## The Why

So by now everyone has heard of [Docker](https://www.docker.com) right?
(*If not, you have some catching up to do!*)

Why have I created this mini-PaaS based around Docker? What's wrong
with the many myriad of platforms and services out there:

- [Tutum](https://www.tutum.co)
- [AWS](http://aws.amazon.com/)
- [Deis](http://deis.io/)
- [Solum](http://solum.io/)
- [Flynn](https://flynn.io/)

Well. Nothing! The various platforms, services and stacks that exist to
service, deploy, monitor applications using Docker all have their
use-cases and pros and cons.

If you call the post [Flynn vs. Deis: The Tale of Two Docker Micro-PaaS
Technologies](http://www.centurylinklabs.com/flynn-vs-deis-the-tale-of-two-docker-micro-paas-technologies/)
i said the following about [\~10months
ago](http://www.centurylinklabs.com/flynn-vs-deis-the-tale-of-two-docker-micro-paas-technologies/#comment-1910702097).

I've stuck by this and 10 months later here it is.

`docker-compose.yml`:

```#!yaml
autodock:
  image: prologic/autodock
  ports:
    - "1338:1338/udp"
    - "1338:1338/tcp"
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock

autodocklogger:
  image: prologic/autodock-logger
  links:
    - autodock

autodockhipache:
  image: prologic/autodock-hipache
  links:
    - autodock
    - hipache:redis

hipache:
  image: hipache
  ports:
    - 80:80
    - 443:443
```

Gist here: <https://gist.github.com/prologic/72ca4076a63d5dd1687d>

This uses the following tools and software (*all which I wrote as well*):

- [autodock](https://github.com/prologic/autodock)
- [autodock-logger](https://github.com/prologic/autodock-logger)
- [autodock-hipache](https://github.com/prologic/autodock-hipache)
- [hipachectl](https://github.com/prologic/hipachectl)

Now. Here's the thing. Nothing here is particularly fancy.

-   There's no DNS management
-   There's no fancy services to speak of
-   There's no web interface at all.
-   There's no API or even a CLI tool

So what is there?

Basically this works in a very simple way.

1. Setup a wildcard A record on a domain pointing it at your Docker host.
1. Spin up containers with the `-e VIRTUALHOST` environment variable.

That's it!

## The How

How this works:

- `autodock` is a daemon that listens for Docker events via the Docker
  Remote API
- `autodock` is pluggable and provides a UDP-based distributed interface to
  other plugins.
- When `autodock` sees a Docker event it broadcasts it to all nodes.
- When `autodock-hipache` sees container
  start/stop/died/killed/paused/unpaused events it:
  - Checks for a `VIRTUALHOST` environment variable.
  - Checks for a `PORT` environment variable (*optional, default: `80`*).
  - Checks the configuration of the container for exposed ports.
  - If `PORT` is a valid exposed port; reconfigure
    [hipache](https://github.com/hipache/hipache) with the provided
    virtualhost from the `VIRTUALHOST` environment variable routing
    web requests to the container's ip address and port given by `PORT`.

## Usage

Using this is quite simple. Copy the above `docker-compose.yml` and run:

```#!bash
$ docker-compose up -d
```

Then start a container:

```#!bash
$ docker run -d -e VIRTUALHOST=hello.local prologic/hello
```

And visit: http://hello.local

Assuming (*of course*) `hello.local` points to your Docker host in `/etc/hosts`.

Of course real deployments of this will use real domains and a real DNS
server.

Two example deployments of this can be seen here:

- <http://hello.vz1.bne.shortcircuit.net.au/>
- <http://hello.vallinux.org/>

Enjoy! :)

----

**Update:** I have now created a new project/tool to help facilitate the
setup of this little minimal PaaS. Check out
[autodock-paas](https://github.com/prologic/autodock-paas)!
