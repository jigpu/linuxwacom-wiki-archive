---
title: Downloads
permalink: wiki/Downloads/
layout: wiki
tags:
 - HOWTO
---

For your tablet to work, two pieces are needed: a **kernel driver** and
a **X11 driver**. Please read our [history](history "wikilink") for a
better understanding of the difference between
[xf86-input-wacom](xf86-input-wacom "wikilink") and the legacy
[linuxwacom](linuxwacom "wikilink").

Kernel drivers
==============

The kernel driver is responsible for understanding hardware-specific
protocols and translating their data into standard kernel input events.
These events can then easily be read by user-space applications (such as
our X11 driver, or [evtest](evtest "wikilink")) without requiring much
hardware-specific knowledge.

Our patches to support Wacom devices are provided upstream to the Linux
kernel developers for integration into the official releases at
[kernel.org](http://www.kernel.org). These changes will usually feed
back into your distribution's kernel. We recommend that you first try to
update your kernel through your distribution's mechanisms. If you
already have the newest kernel available for your distribution,
backported kernel drivers are below.

To determine your kernel version, open a terminal and run the following
command: `uname -r`

| Linux Kernel    | Download                                 |
|-----------------|------------------------------------------|
| 3.0 - CURRENT   | Please update through your distribution. |
| 2.6.26 - 2.6.40 | [input-wacom](input-wacom "wikilink")    |
| 2.6.16 - 2.6.25 | [linuxwacom](linuxwacom "wikilink")      |

X11 drivers
===========

The X driver is responsible for translating the input events generated
by the kernel driver into XInput events that the X server can understand
and relay to running applications. It is also responsible for
virtualizing the single hardware device to appear as multiple logical
devices (e.g. stylus, eraser, touch). This grants applications like GIMP
access to extended functionality like pressure and high-resolution
coordinates.

Distributions usually already ship a version of the X11 driver in their
repositories. We recommend that you first try to update through your
distribution's mechanisms. If you require an even more up-to-date
version, the latest X11 drivers are below.

To determine your XOrg server version, open a terminal and run the
following command: `X -version`

| XOrg Server   | Download                                        |
|---------------|-------------------------------------------------|
| 1.7 - CURRENT | [xf86-input-wacom](xf86-input-wacom "wikilink") |
| 1.4 - 1.6     | [linuxwacom](linuxwacom "wikilink")             |

Miscellaneous
=============

The following links may be of interest to some users. Please be aware
that some links may be to projects which are neither developed nor
supported by the Linux Wacom Project.

-   [wdaemon](wdaemon "wikilink"): Linux Wacom Hotplug Daemon
-   [OpenSolaris STREAMS
    module](http://hub.opensolaris.org/bin/view/Community+Group+device_drivers/wacomtablet)
-   [Wacom Control
    Panel](http://gtk-apps.org/content/show.php/Wacom+Control+Panel?content=104309)
    (GNOME 2.x only, GNOME 3 has a wacom-specific control panel applet)
