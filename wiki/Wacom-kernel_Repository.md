---
title: Wacom-kernel Repository
permalink: wiki/Wacom-kernel_Repository/
layout: wiki
tags:
 - DeveloperPages
---

The wacom-kernel git repository is a recent reflection of the current
[linux kernel](http://kernel.org/), and is intended to be used to assist
Wacom kernel module development. To check how closely synchronized
wacom-kernel is with the **mainline** kernel select *Gitweb* to browse
the kernel's git repository.

wacom-kernel
------------

The git repository is available to [browse
here](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/wacom-kernel).
It consists of 3 branches: devel, master, and tested.

To clone the wacom-kernel git repository use:

    git clone git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/wacom-kernel

Working with a Wacom Module
---------------------------

The devel branch is intended for working with the modules. Use *git
checkout* to select it:

    cd wacom-kernel
    git checkout devel

Depending on the module you wish to work with change to the appropriate
directory.

-   wacom.ko files: wacom.h, wacom\_sys.c, wacom\_wac.c, wacom\_wac.h
    -   in drivers/input/tablet
-   hid-wacom.ko file: hid-wacom.c
    -   in drivers/hid
-   wacom\_w8001.ko file: wacom\_w8001.c
    -   in drivers/input/touchscreen

Make your changes. There is a Makefile in the wacom-kernel/wacom-kernel
directory that makes it a bit easier to compile & install the wacom
modules. Change directory to /wacom-kernel and use:

    make modules
    make install

Sample Workflow
---------------

A possible workflow could consist of the following:

1.  Make changes to the module code.
2.  Add the changed file with *git add* and sometimes do a *git commit*.
3.  Then cd to wacom-kernel (use separate terminal for that) and *make
    modules* (might use the alternative *make bmodules* if working on
    hid-wacom).
4.  If the compilation goes OK do a *make install* from a separate
    terminal with root account. (could use *make btinstall* if you used
    *make bmodules* in 3).
5.  Now *rmmod* the new module and after restarting the tablet the new
    module is loaded.
6.  Test if the changes worked.

Maintainer
----------

The repository is maintained by Przemo Firszt.
