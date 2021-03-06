---
title: Category:DeveloperPages
permalink: wiki/Category:DeveloperPages/
layout: tagpage
tag: DeveloperPages
---

For working tablet support, you need two vital pieces installed on your
machine. A kernel driver that recognises the tablet and understands the
hardware protocol, and an X driver that allows X to use the device.

X driver development
====================

[xf86-input-wacom](xf86-input-wacom "wikilink") is in a git repository.
To develop, the following steps are required:

-   [clone](/wiki/Using_Git "wikilink") the source

` git clone `[`git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom`](git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom)

-   [build](/wiki/Building_the_driver "wikilink") the source

` ./autogen.sh --prefix=/usr`

-   hack it!
-   create a patch with [git format-patch](/wiki/Using_Git "wikilink")
-   submit the patch to the [linuxwacom-devel mailing
    list](https://lists.sourceforge.net/lists/listinfo/linuxwacom-devel)

For more information on these steps, see

-   [Using Git](/wiki/Using_Git "wikilink")
-   [Building the driver](/wiki/Building_the_driver "wikilink")
-   [Submitting Patches](/wiki/Submitting_Patches "wikilink")

Developing for linuxwacom
-------------------------

linuxwacom is a collection of backports for both older kernels and older
X servers. The linuxwacom repository is maintained in CVS and releases
are made regularly. If you want to propose a patch, please follow the
instructions [ here](/wiki/LinuxwacomDevelopment "wikilink")

Kernel driver development
=========================

For Wacom kernel driver development, please submit your patches to the
[Linux Kernel Developers Mailing List](http://lkml.org). Only patches
that appear in the upstream kernel will be appear in distributions and
thus ensure tablets will be working out-of-the-box with future releases
of your distribution.

**FIXME** more information needed here.

Note that kernel patches integrated into the
[linuxwacom](linuxwacom "wikilink") tarball usually do not find their
way upstream and thus need to be re-implemented or forward-ported for
future kernels. Additionally, kernel patches in linuxwacom are not
shipped by any major distribution. Any kernel developer is encouraged to
work on the [upstream kernel](http://kernel.org) instead.

Developer Pages
===============

All pages with information related to development are part of the
Category *DeveloperPages*. See below for a list of these pages.
