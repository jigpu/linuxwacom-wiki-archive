---
title: Input-wacom
permalink: wiki/Input-wacom/
layout: wiki
tags:
 - DeveloperPages
---

The **input-wacom** driver is the usb kernel driver/module paired with
[xf86-input-wacom](xf86-input-wacom "wikilink"). It backports new
features and model support to kernels 2.6.26 through 2.6.36. Those with
kernels 2.6.37 or higher should use their distro supplied kernel
drivers.

**If you are developing support for new devices, do not use this
package. Develop against upstream kernels instead.** Patches in
input-wacom may not find their way into upstream kernels. Any backported
kernel driver features and model support should be submitted to the
[linux-input mailing
list](https://patchwork.kernel.org/project/linux-input/).

Getting the Source
------------------

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/input-wacom/)
and unpack it with:

    tar xjvf input-wacom-<version number>.tar.bz2
    cd input-wacom-<version number>

Alternatively, pre-release code is available at the [input-wacom git
repository](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/input-wacom;a=summary).

    git clone git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/input-wacom
    cd input-wacom

Building the Driver
-------------------

We provide a helper script *autogen.sh* that wraps the autotools steps.
First make sure you have the following tools installed: gcc, automake,
autoconf and make.

    ./autogen.sh --prefix=/usr --libdir=/usr/lib    # on 32 bit machines
    ./autogen.sh --prefix=/usr --libdir=/usr/lib64  # on 64 bit machines

Installing the Driver
---------------------

Follow the instructions provided at the end of the build to copy the
kernel modules to your system's module directory. USB tablets rely on
the `wacom.ko` kernel module, while most touchscreens will use the
`wacom_w8001.ko` module:

    cp ./<kernel version>/wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet/
    cp ./<kernel version>/wacom_w8001.ko /lib/modules/`uname -r`/kernel/drivers/input/touchscreen/

After the modules have been copied, you will need to rebuild all of the
module dependencies:

    depmod -a

X driver
--------

Upgrading both the kernel driver and the X driver to the same version is
recommended. Please see [xf86-input-wacom](xf86-input-wacom "wikilink")
for the X driver required for X servers 1.7 and later.
