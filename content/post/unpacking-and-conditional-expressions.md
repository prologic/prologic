---
title: "Unpacking and Conditional Expressions"
subtitle: "Unpacking tuples and conditional expressions in Python."
date: 2013-02-17T13:20:00+10:00
categories:
  - Development
  - Python
tags:
  - Python
  - Unpacking
  - Conditional
  - Expressions
  - Tuples
draft: false
---

The other day whilst working on a web crawler at work called
[spyda](http://bitbucket.org/prologic/spyda/) I came across an interesting
behavior in Python. Here it goes:

```#!python
>>> xs = [(1, 2), (3, 4)]
>>> a, b = xs[0] if xs else None, None
>>> a
(1, 2)
>>> b
```

Notice that `a` becomes the first tuple in the list and `b` becomes `None`?
This kinda got me stumped at first until I realized what was happening with
the Python parser. If however you create your expression like this:

```#!python
>>> a, b = xs[0] if xs else (None, None)
>>> a
1
>>> b
2
>>> 
```

Then everything is all good!

The lesson here (*I think*) is to use parenthesis here to both make things
clearer to the reader and to ensure the correct expression evaluation.
