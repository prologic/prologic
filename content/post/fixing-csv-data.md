---
title: "Fixing CSV Data with csvkit"
date: 2014-01-16T12:48:00+10:00
categories:
  - Development
  - Python
tags:
  - Python
  - CSV
  - Data
  - csvkit
draft: false
---

TL;DR: [csvkit](https://pypi.python.org/pypi/csvkit) is a great little library
and set of UNIX-style utilities for dealing with CSV data. Here I show
how I solved a problem with some CSV Data I had at work and how I solved
it with [Python](http://www.python.org/) and
[csvkit](https://pypi.python.org/pypi/csvkit)!

## Problem

Basically the problem boiled down to the classical
[GIGO](http://en.wikipedia.org/wiki/Garbage_in,_garbage_out) problem
where I had a CSV Input Source containing inconsistent data as well as
inconsitent formatting (*superfluous whitespace*).

In fact the CSV Input Source originally came from a Microsoft Excel
Spreadsheet which itself came from a MySQL Dump of a Drupal Website :/

    Drupal Site -> MySQL Dump -> Excel -> CSV

Sadly this is the no. of steps the data took to get to me where I began
the arduous task of cleaning it up and writing an importer for it for our
Plone Site.

One problem I had in particular was inserting a new column of slugified
URL(s) based on the `Title` field.

#3 Solution

```#!bash
$ pip install csvkit slugify
$ csvcut -x -e utf-8 -c 1,3 -S < nccarf.csv > titles.csv
$ csvpy -e utf-8 titles.csv
Welcome! "titles.csv" has been loaded in a CSVKitReader object named "reader".
```

Here we:

1. Install [csvkit](https://pypi.python.org/pypi/csvkit) and
   [slugify](https://pypi.python.org/pypi/slugify).
1. Extract columns 1 and 3 (*Node ID and Title*) from the input CSV
   using `csvkit`.
1. Start a `csvpy` interactive session to inspect the resulting data.

```#!python
>>> from slugify import slugify
>>> xs = list(reader)
>>> slugify(xs[1][1])
'nccarf-brochure'
>>> ys = [xs[0]] + [[x[0], slugify(x[1])] for x in xs[1:]]
>>> ys[0]
[u'Node ID', u'URL']
>>> ys[1]
[u'54', 'nccarf-brochure']
>>> ys[2]
[u'61', 'an-assessment-of-the-vulnerability-of-australian-forests-to-the-impacts-of-climate-change-two-page-summary-of-key-findings']
>>> ys[3]
[u'568', 'nccarf-strategic-plan-a-summary']
>>> ys[4]
[u'862', 'climate-change-adaptation-research-in-australia-an-overview-of-research-funded-by-nccarf']
>>> header = ys[0]
>>> rows = ys[1:]
>>> import csv
>>> with open("urls.csv", "wb") as f:
...     writer = csv.writer(f, header)
...     writer.writerow(header)
...     writer.writerows(rows)
...
>>> ^D
```

Here we:

1. Import the `slugify` function from the
   [slugify](https://pypi.python.org/pypi/slugify) module.
2. Convert the `reader` into a `list` of `xs` so we can manipulate the list.
3. Test "slugifying" a sample of the data.
4. Generate a list of `ys` where each **Title** (*indexed at 1*) is
   "slugified" using the `slugify` function.
5. Check the resulting new data in `ys`.
6. Write out a new `urls.csv` output CSV which we will later join with
   the original input CSV (`nccarf.csv`).

```#!bash
$ csvjoin -c 1,1 -e utf-8 --left nccarf.csv urls.csv | csvcut -e utf-8 -C 18 > nccarf.csv
```

This is where the final bit of the "magic" happens. `csvjoin` from the
[csvkit](https://pypi.python.org/pypi/csvkit) set of tools is a
wonderful thing for creating new CSV based data sets from two or more
pieces of input CSV data.

This basically performs a left inner join on column one of the two input
csv files and pipes the output to a new resulting output file.

Enjoy! :)
