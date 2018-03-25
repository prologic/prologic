---
title: "AsyncIO and Protocols"
subtitle: "Python AsyncIO and Networking Protocols"
date: 2014-04-02T12:46:00+10:00
categories:
  - Development
  - Python
  - Rant
tags:
  - Python
  - AsyncIO
  - PyPi
draft: false
---


{{% warning %}}
This is a bit of a rant regarding [Python](http://www.python.org/)'s
[AsyncIO](https://docs.python.org/3.4/library/asyncio.html) and
Networking Protocols.
{{% /warning %}}

Lately I'm seeing an influx of re-implementations of various networking
protocols hit the [Python Package Index](https://pypi.python.org/pypi)
that implement said protocols for the new [Python](http://www.python.org/)
[AsyncIO](https://docs.python.org/3.4/library/asyncio.html) standard library
module (*framework*)? for Python 3.4+

One of the most frustrating things about being the developer and maintainer
behind [circuits](http://circuitsframework.com/) is the fact that there are
very few good framework agnostic implementations of low-level networking
protocols. There are some exceptions:

- [dnslib](https://pypi.python.org/pypi/dnslib)
- [http-parser](https://github.com/benoitc/http-parser)
- *and probably others\...* -- But I haven't come across too many!

This "lack of framework agnostic" networking protocol libraries is one
of the largest reasons why [circuits](http://circuitsframework.com/)
does not implement or support many commonly found networking protocols
such as those found in the [Twisted](https://twistedmatrix.com/)
framework.

I can only hope this will change and this sparks an interest in other
Pythonistas to improve this situation.
