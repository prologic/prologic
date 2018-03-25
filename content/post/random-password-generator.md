---
title: "Random Password Generator"
date: 2013-02-21T22:03:00+10:00
categories:
  - Development
  - Python
tags:
  - Python
  - Password
  - Random
  - Generator
draft: false
---

I've been seeing a lot of this lately on
[ActiveState Code Recipes](http://code.activestate.com/); Random Password
Generators written in [Python](http://www.python.org). Well I thought I'd
get in on this and share one I've been using for many years, but mostly
because I'm finding "Blogging" to be quite fun!

This recipe is also a part of my very old
[pymills](http://bitbucket.org/prologic/pymills/) repository of useful
modules and routines I've either written or collected. You might find
bits and pieces of my library useful. If you do please let me know!

**UPDATE**: Fixed some bugs...

```#!python
#!/usr/bin/env python

import string
from time import time
from itertools import chain
from random import seed, choice, sample


def mkpasswd(length=8, digits=2, upper=2, lower=2):
    """Create a random password

    Create a random password with the specified length and no. of
    digit, upper and lower case letters.

    :param length: Maximum no. of characters in the password
    :type length: int

    :param digits: Minimum no. of digits in the password
    :type digits: int

    :param upper: Minimum no. of upper case letters in the password
    :type upper: int

    :param lower: Minimum no. of lower case letters in the password
    :type lower: int

    :returns: A random password with the above constaints
    :rtype: str
    """

    seed(time())

    lowercase = string.lowercase.translate(None, "o")
    uppercase = string.uppercase.translate(None, "O")
    letters = "{0:s}{1:s}".format(lowercase, uppercase)

    password = list(
        chain(
            (choice(uppercase) for _ in range(upper)),
            (choice(lowercase) for _ in range(lower)),
            (choice(string.digits) for _ in range(digits)),
            (choice(letters) for _ in range((length - digits - upper - lower)))
        )
    )

    return "".join(sample(password, len(password)))
```

This recipe is also available here:

- http://code.activestate.com/recipes/578468-password-generator-mkpasswd/

Enjoy! :)
