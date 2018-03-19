---
title: "Vmware Tools with 64bit Linux Guest"
date: 2011-01-24T00:00:00+10:00
categories:
  - infrastructure
tags:
  - VMWare
  - amd_64
  - Linux
  - CRUX
draft: false
---

New job, new challenges, new beginnings. One of the things I've had to do as
part of my new job is to build a base system to develop our new product on.
We chose to use Vmware Fusion as we run iMac's as development machines. 

My preferred Linux distribution happens to be [CRUX](https://crux.nu) as it's
lightweight, easy to maintain and "cruft-less".  The down-side (if you can
call it that) is we (*the CRUX community*) don't have that many `x86_64` users
so our `x86_64` support is somewhat lacking. It's there, but could do with
more attention/work. 

That said, I installed a fresh CRUX-2.7 (*the latest at the time of writing
this article*); the `x86_64` version into a virtual machine powered by VMware
Fusion. Everything went smoothly after figuring out all the right kernel
drivers. Except for vmware-tools.

vmware-tools (*after spending 2 days trying to make it work*) relies on perl's
configuration to work out what kind of platform it's on. Specifically it
relies on "archname" (*See: `perl -V`*). 

As I said earlier, `x86_64` support in CRUX is somewhat limited and as it
turned out we don't really have a `x86_64` version of the perl port that's
maintained in the 32bit distribution (until today). 

So all-in-all, if anyone reads this article or comes across the same problem;
recompile perl with `-Darchname=x86_64-linux` 

For those that do use CRUX (*and the `x86_64` version*) the `x86_64` perl port
is here: 

https://bitbucket.org/prologic/ports/src/de2f0dcd650e/x86_64/perl/ 
