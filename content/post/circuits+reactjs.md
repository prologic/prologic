---
title: "circuits + React"
subtitlte: "circuits + ReactJS Tutorial"
date: 2015-06-02T21:52:00+10:00
categories:
  - Tutorial
  - Circuits
  - Python
tags:
  - Python
  - Circuits
  - React
draft: false
---

I've been hearing lots of good things about
[React](https://facebook.github.io/react/) (*a JavaScript library to
build User Interfaces*) and recently came across the several
[tutorials](https://github.com/reactjs/react-tutorial) that have a
backend implementation in:

- [PHP](https://github.com/reactjs/react-tutorial/blob/master/server.php)
- [Python](https://github.com/reactjs/react-tutorial/blob/master/server.py)
- [JavaScript](https://github.com/reactjs/react-tutorial/blob/master/server.js)
- [Ruby](https://github.com/reactjs/react-tutorial/blob/master/server.rb)
- [Go](https://github.com/reactjs/react-tutorial/blob/master/server.go)

I felt that there was one missing in particular and that of course is:

- [circuits](https://github.com/circuits/circuits)

For those not familiar with [circuits](https://github.com/circuits/circuits)
it is:

- A Python Application Framework
- Supports a loosely coupled architecture
- Is fully event-driven (*i.e: react!*)
- Supports Asynchronous I/O
- Supports Coroutines
- and more more\...

Here is the same implementation of the above backends using
[circuits](https://github.com/circuits/circuits):

```#!python
from os import environ
from json import dumps, loads

from circuits.web import Server, Controller, Static


class Comments(Controller):

    channel = "/comments.json"

    def index(self, **data):
        with open("comments.json", "r") as f:
            comments = loads(f.read())

        if self.request.method == "POST":
            comments.append({"author": data["author"], "text": data["text"]})

            with open("comments.json", "w") as f:
                f.write(dumps(comments))

        self.response.headers["Content-Type"] = "application/json"
        self.response.headers["Cache-Control"] = "no-cache"

        return dumps(comments)


(Server(("0.0.0.0", int(environ.get("PORT", 3000)))) + Comments() + Static(docroot="public")).run()
```

Now whilst this may seem very similar to the
[Python+Flask](https://github.com/reactjs/react-tutorial/blob/master/server.py)
example using [circuits](https://github.com/circuits/circuits) has
several advantages:

- We can *easily* add new functionality by "composing" and "registering"
  more Component(s)

Let's say we want to password protect our resource without modifying
the existing code?

Easy:

Write a `Auth` Component:

```#!python
from hashlib import md5

from circuits import handler, Component
from circuits.web.tools import check_auth, basic_auth


class Auth(Component):

    channel = "web"

    realm = "Comments"
    users = {"admin": md5("admin").hexdigest()}

    @handler("request", priority=1.0)
    def on_request(self, event, request, response):
        if request.path != "/comments.json":
            return

        if not check_auth(request, response, self.realm, self.users):
            event.stop()
            return basic_auth(request, response, self.realm, self.users)
```

And add this newly created component into the mix:

```#!python
(Server(("0.0.0.0", int(environ.get("PORT", 3000)))) + Auth() + Comments() + Static(docroot="public")).run()
```

Enjoy! And happy hacking! :)
