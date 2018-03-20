---
title: "Python Websockets Server with Circuits"
date: 2011-02-27T09:50:00+10:00
categories:
  - Development
  - Python
tags:
  - WebSockets
  - circuits
  - Python
draft: false
---

Today marks the release of
[circuits-1.5](http://pypi.python.org/pypi/circuits/1.5) which brings to the
[circuits](http://bitbucket.org/prologic/circuits/) application framework a
new `circuits.web` dispatcher.

## The **WebSockets** dispatcher

This dispatcher allows you to build a web application that is capable of
accepting WebSockets client connections on the same process/thread.

Using circuits-1.5 here is a very simple Web Application that has WebSockets
support and echos back every message it recieves:

`index.html`:

```#!html
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en-US" lang=
"en-US">
<head>
  <title>circuits.web WebSockets Demo</title>
  <meta http-equiv="content-type" content=
  "application/xml+xhtml; charset=us-ascii" />
</head>

<body>
//<![CDATA[
     function test() {
         var ws = new WebSocket("ws://" + window.location.host + "/websocket");
         ws.onopen = function(e) {
             console.log("Opened");
             console.log(e);
             ws.send("Hello World!");
         };
         ws.onerror = function (e) {
             console.log("Error");
             console.log(e);
         };
         ws.onmessage = function (e) {
             console.log("Message");
             console.log(e.data);
         };
         ws.onclose = function(e) {
             console.log("Closed");
             console.log(e);
         };
         return ws;
     }
  //]]>
  </script>
</body>
</html>
```

`server.py`:

```#!python
#!/usr/bin/env python

from circuits.net.sockets import Write
from circuits.web import Logger, Server, Static
from circuits.web.dispatchers import WebSockets
from circuits import handler, BaseComponent, Component, Event

from circuits import Debugger

class Echo(Component):

    channel = "ws"

    def message(self, sock, data):
        self.push(Write(sock, data))

(Server(("0.0.0.0", 8000))
    + WebSockets("/websocket")
    + Debugger()
    + Static()
    + Logger()
    + Echo()
).run()
```

Try running this example in Chrome. Bring up the Developer Tools -> Console
and type into the console the following:

```#!javascript
ws = test();
ws.send("Hello World!");
```

Have fun!
