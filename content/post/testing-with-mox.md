---
title: "Testing With Mox"
date: 2010-10-24T00:00:00+10:00
categories:
  - Development
  - Testing
tags:
  - Python
  - mox
  - circuits
draft: false
---

Last week I discovered the mocking library
[mox](http://code.google.com/p/pymox/). I had previously known about the
concept of mocking but had no experience in writing tests that mocked anything
out... We use mocks quite a bit where I work and it was while implementing new
functionality into our software that I learned also to write tests that used
mocks (using the mox library/framework). 

The most challenging concept with mocking in general was deciding what to mock
and what not to mock and how you should structure your test. 

So let's write a simple function that gives us the uptime of the host we're
running on and extract just the time that the host has been up for. Then let's
write a test that demonstrates using mox and mocking out something... 

```#!python
import re 
import subprocess 
from StringIO import StringIO 

import mox 

TEST_DATA = "13:00 up 5:03, 4 users, load averages: 0.16 0.22 0.23\n" 

def getuptime(): 
    p = subprocess.Popen(["uptime"], stdout=subprocess.PIPE) 
    p.wait() 
    s = p.stdout.read() 
    m = re.search("up\W+(.*?),", s) 
    if m: 
        return m.group(1) 

def pytest_funcarg__mock(request): 
    mock = mox.Mox() 
    request.addfinalizer(lambda : mock.UnsetStubs()) 
    return mock 

def test(mock): 
    mock.StubOutWithMock(subprocess, "Popen", True) 

    p = mock.CreateMockAnything() 
    p.stdout = StringIO(TEST_DATA) 

    subprocess.Popen(mox.IgnoreArg(), stdout=mox.IgnoreArg()).AndReturn(p) 

    p.wait() 

    mock.ReplayAll() 

    uptime = getuptime() 

    assert uptime == "5:03" 

    mock.VerifyAll() 
```

Runnaing this produces:

```#!bash
$ py.test uptime.py 
============================= test session starts ============================== 
platform darwin -- Python 2.6.1 -- pytest-1.3.4 
test path 1: uptime.py 

uptime.py . 

=========================== 1 passed in 0.02 seconds =========================== 
```

So the thing to note here is that we're mocking out the Popen system as we
don't care about testing this. We only want to test our parsing logic,
the code that we wrote. 

There are several tests in my
[circuits framework](http://bitbucket.org/prologic/circuits/) that could
greatly benefit from having mocked objects.  
