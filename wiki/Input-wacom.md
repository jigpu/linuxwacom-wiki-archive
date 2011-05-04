---
title: Input-wacom
permalink: wiki/Input-wacom/
layout: wiki
tags:
 - DeveloperPages
---

The input-wacom driver is the usb kernel driver/module paired with
[xf86-input-wacom](xf86-input-wacom "wikilink"). Intended for developers
and testers only. It provides backward support for kernels 2.6.26 to
2.6.37. Those with kernels 2.6.36 or higher should use their distro
supplied kernel drivers. Any new kernel driver features and model
support should be submitted to the [linux-input mailing
list](https://patchwork.kernel.org/project/linux-input/).

**If you are developing support for new devices, do not use this
package. Develop against upstream kernels instead.** Patches in
input-wacom may not find their way into upstream kernels.

Download
--------

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/input-wacom/).
The input-wacom folder is in the xf86-input-wacom folder. There is a git
repository for
[input-wacom](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/input-wacom;a=summary).

Installing from the tarball
---------------------------

In the following, you need to replace the version number with the one
from your downloaded tarball.

    tar xjvf input-wacom-0.11.0.tar.bz2
    cd input-wacom-0.11.0
    ./autogen.sh --prefix=/usr --libdir=/usr/lib  # on 32 bit machines
    ./autogen.sh --prefix=/usr --libdir=/usr/lib64  # on 64 bit machines

Then copy the wacom.ko module to the appropriate directory. Depending on
your kernel you may need to locate the folder the compiled wacom.ko is
in and modify the copy command accordingly.

    cp ./2.6.30/wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet/wacom.ko

Rebuild all of the module dependencies.

    depmod -a

X driver
--------

Please see [Xf86-input-wacom](/wiki/Xf86-input-wacom "wikilink") for the X
driver required for X servers 1.7 and later.
