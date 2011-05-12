---
title: History
permalink: wiki/History/
layout: wiki
---

"linuxwacom" stands for two things. It is the project name used for
wacom-specific implementations (short for "The Linux Wacom Project"). It
is also the name of the driver release shipped by the project. The Linux
Wacom Project was created in 2002 by John Joganic. The code was kept in
CVS and regularly released in the form of a tarball that included kernel
drivers, X drivers and various utilities. The main (and long-term)
contributor to [linuxwacom](linuxwacom "wikilink") is still Ping Cheng,
a Wacom employee. Wacom naturally has a strong focus on its customers
and thus linuxwacom goes into some pains supporting systems all the way
back to XFree86-4. However, the [X server](http://www.x.org) has moved
quite a bit.

In August 2009, Peter Hutterer split out the X11 driver from the
linuxwacom package and imported it into a git repository called
[xf86-input-wacom](xf86-input-wacom "wikilink"). This driver has seen
quite a bit of rework, dropping of some features (e.g. XFree86-4
support) and gaining others (e.g. input device properties). All modern
distributions now ship [xf86-input-wacom](xf86-input-wacom "wikilink")
as their X driver for Wacom tablets. Kernel-level device support is
developed upstream on the [Linux Kernel Mailing List](http://lkml.org).

However, some (mainly enterprise) users need to run specific kernels or
X server versions. For these, the Linux Wacom Project provides ongoing
support for the [linuxwacom](linuxwacom "wikilink") tarballs and a newer
tarball called [input-wacom](input-wacom "wikilink"). Both of these
package include kernel *backports* for certain kernel versions. Desktop
distribution users usually do not need either tarball and should rely on
their distribution packages instead.

Some more information is available here:
<http://who-t.blogspot.com/2010/09/wacom-support-in-linux.html>
