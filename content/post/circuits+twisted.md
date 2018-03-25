---
title: "circuits + twisted"
subtitle: "circuits + twisted integration"
date: 2013-08-24T15:01:00+10:00
categories:
  - Development
  - Python
  - Circuits
tags:
  - Python
  - Circuits
  - Twisted
draft: false
---

Well I've finally gone and done it! It's still alpha at this stage but twisted
applications, protocols and libraries can now run on top of circuits.

Check out the code and samples:

- https://bitbucket.org/circuits/twistedintegration

All you need to do is install the CircuitsReactor at the top of your module:

```#!python
import circuitsreactor
circuitsreactor.install()
```

Here's a simple "Echo Server" example straight from twisted's docs:

```#!python
#!/usr/bin/env python

import circuitsreactor
circuitsreactor.install()

from twisted.internet import reactor
from twisted.internet.protocol import Factory, Protocol
from twisted.internet.endpoints import TCP4ServerEndpoint


class Echo(Protocol):

    def dataReceived(self, data):
        self.transport.write(data)


class EchoFactory(Factory):

    def buildProtocol(self, addr):
        return Echo()

# 8007 is the port you want to run under. Choose something >1024
endpoint = TCP4ServerEndpoint(reactor, 8007)
endpoint.listen(EchoFactory())
reactor.run()
```

Enjoy :)
