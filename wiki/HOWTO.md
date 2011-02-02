---
title: HOWTO
permalink: wiki/HOWTO/
layout: wiki
---

Introduction
============

This document began in November 2002 as a HOWTO for setting up a USB
Wacom Intuos2 on Redhat 8.0. It has since grown to cover all Wacom
tablets, USB and serial, running on various different Linux
distributions. As of December 2002, this project has transformed into
the Linux Wacom Project. Work on this document is on-going so if you
find an error, have a question, or have something to add, please send an
email to: linuxwacom-discuss@lists.sf.net.

How To Use This Document
------------------------

Where the guide differs between distributions or packages, I have added
a comment which appears indented with a light blue background.

Also, it bears mentioning since it's a detail often missed: there are
two drivers in the Linux Wacom Project- wacom.o and wacom\_drv.o. The
first driver is the USB kernel driver. The second driver is the XFree86
Wacom driver. Serial users need only be concerned with the wacom\_drv.o
driver. USB users need both. If you try to use the wacom\_drv.o driver
in place of the wacom.o kernel driver or visa-versa, things generally
won't work.

Next, this document was written with the assumption that you are
starting from scratch with a relatively recent distribution of the Linux
kernel. Also, if you have already added lines to your XFree86/Xorg
configuration file (XF86Config/XF86Config-4 or xorg.conf), you should
comment them out and restart X. Since we'll be stepping through the
entire process, we need X to ignore the tablet until we're ready.
Otherwise, X will just get in the way.

Finally, if you know what you're doing, you can leave your X settings
intact, print this out, switch to runlevel 3, and follow along from the
console. An HTML version of this document can be found in the docs
directory at docs.html.

Wacom Driver Theory of Operation
================================

See [How Wacom tablets work](/wiki/How_Wacom_tablets_work "wikilink")

Getting It Together
===================

See [Building linuxwacom](/wiki/Building_linuxwacom "wikilink")

Next: The [USB Kernel driver](/wiki/USB_Kernel_driver "wikilink")
