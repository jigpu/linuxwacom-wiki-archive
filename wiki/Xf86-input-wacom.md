---
title: Xf86-input-wacom
permalink: wiki/Xf86-input-wacom/
layout: wiki
---

The xf86-input-wacom driver is the wacom-specific X11 input driver for
the X.Org X Server version 1.7 and later (X11R7.5 or later).

### Installing from git

    git clone git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom
    cd xf86-input-wacom
    ./autogen.sh --prefix=/usr
    make && make install

This gets the latest version from git and installs it into the given
prefix. In most cases, the prefix of /usr is the right one. If you're on
a 64-bit machine, configure with --libdir=/usr/lib64. If unsure, search
for evdev\_drv.so. If it is in /usr/lib/xorg/modules/input, then the
prefix of /usr is enough. If it is in /usr/lib64/xorg/modules/input,
then you need to specify --libdir=/usr/lib64.

Once installed, you need to restart the X server. Either hit
Ctrl+Alt+Backspace (if enabled), log out or reboot, whichever you
prefer.
