---
title: Input-wacom
permalink: wiki/Input-wacom/
layout: wiki
tags:
 - DeveloperPages
---

The input-wacom driver is the usb kernel driver/module paired with
xf86-input-wacom. Intended for developers and testers only. It provides
backward support for kernels 2.6.26 to 2.6.37. Those with kernels 2.6.36
or higher should use their distro supplied wacom.ko. Any new kernel
driver features and model support should be submitted to the
[linux-input mailing
list](https://patchwork.kernel.org/project/linux-input/).

Download
--------

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/). The
input-wacom folder is in the xf86-input-wacom folder.

Installing from the tarball
---------------------------

In the following, you need to replace the version number with the one
from your downloaded tarball.

    tar xjvf input-wacom-0.10.10-1.tar.bz2
    cd input-wacom-0.10.10-1
    ./autogen.sh --prefix=/usr

The prefix of /usr is correct on most distributions (unfortunately, the
default is still /usr/local which isn't used widely anymore). If you're
on a 64-bit machine, you may also need to add --libdir=/usr/lib64. If
unsure, search for evdev\_drv.so. If it is in
/usr/lib/xorg/modules/input, then the prefix of /usr is enough. If it is
in /usr/lib64/xorg/modules/input, even if it is in
/usr/lib/xorg/modules/input, then you need to specify
--libdir=/usr/lib64.

Then copy the wacom.ko module to the appropriate directory. Depending on
your kernel you may need to locate the folder the compiled wacom.ko is
in and modify the copy command accordingly.

    cp ./2.6.30/wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet/wacom.ko

Rebuild all of the module dependencies.

    depmod -a

X driver
--------

Please see [Xf86-input-wacom](/wiki/Xf86-input-wacom "wikilink") for the X
driver required for X servers 1.7 and up.
