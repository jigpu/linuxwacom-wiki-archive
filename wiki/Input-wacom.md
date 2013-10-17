---
title: Input-wacom
permalink: wiki/Input-wacom/
layout: wiki
tags:
 - DeveloperPages
---

The **input-wacom** driver is the usb kernel driver/module paired with
[xf86-input-wacom](xf86-input-wacom "wikilink"). It backports new
features and model support to kernels as old as 2.6.30. You should not
usually need to use this driver in most circumstances, though it may be
useful for recently-announced tablets or older distributions.

**If you are developing support for new devices, please develop against
upstream kernels first.** Patches in input-wacom may not find their way
into upstream kernels. Once a device is supported upstream, feel free to
submit patches for backported support to the [linux-input mailing
list](https://patchwork.kernel.org/project/linux-input/).

Getting the Source
------------------

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/input-wacom/)
and unpack it with:

    tar xjvf input-wacom-<version number>.tar.bz2
    cd input-wacom-<version number>

Alternatively, pre-release code is available at the [input-wacom git
repository](http://sourceforge.net/p/linuxwacom/input-wacom/ci/master/tree/).

    git clone git://git.code.sf.net/p/linuxwacom/input-wacom linuxwacom-input-wacom
    cd linuxwacom-input-wacom

Building the Driver
-------------------

When compiling from a released version of input-wacom, gcc needs to be
installed as well as linux kernel development headers. Examples of
installing kernel headers for a couple of system types:

    apt-get install linux-headers-$(uname -r)  # on Debian machines
    yum install gcc kernel-devel               # on Fedora machines

Configuring and compiling the input driver is accomplished with a single
command.

    ./configure

If compiling from a git clone, the configure script will not exist and
you will need autoconf and automake tools to be installed to generate
the support files. We provide a helper script *autogen.sh* that wraps
executing the autotools steps as well as executing the configure step.

    ./autogen.sh

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

Finally, you'll need to have the module loaded by the kernel so that it
can be used by the rest of the system. The easiest way to do this is to
simply reboot; the kernel will automatically load the newly-installed
module when necessary. Alternatively, you can use the commands
`modprobe -r wacom` and `insmod /path/to/wacom.ko` to force the running
kernel to reload the module without rebooting.

X driver
--------

Upgrading both the kernel driver and the X driver to the same version is
recommended. Please see [xf86-input-wacom](xf86-input-wacom "wikilink")
for the X driver required for X servers 1.7 and later.
