---
title: Input-wacom
permalink: wiki/Input-wacom/
layout: wiki
tags:
 - DeveloperPages
---

The **input-wacom** driver is a set of loadable kernel modules which
allows new tablets to be used with systems using kernels as old as Linux
2.6.30. While this gets many of the updates that are pushed to the
upstream Linux kernel driver, some differences may exist. This will be
particularly apparent for users of older kernels. Because of this, we
recommend upgrading your kernel if possible and only installing this
driver when absolutely necessary.

This driver builds two seperate kernel modules:
[wacom.ko](wacom.ko "wikilink") and
[wacom\_w8001.ko](wacom_w8001.ko "wikilink"). The former is used with
tablets that interface over the USB bus, which accounts for the vast
majority of tablets. The latter is used for older tablet PCs whose
digitizers are connected via a serial interface. The
[hid-wacom.ko](hid-wacom.ko "wikilink") and
[wacom\_i2c.ko](wacom_i2c.ko "wikilink") modules (for Bluetooth and I2C
tablets, respectively) are *not* included with this driver -- updates to
these are only available through kernel updates.

Installation
------------

### Prerequisites

Before building, you'll need to ensure the necessary dependencies have
been installed on your system:

<code>

`sudo apt-get install linux-headers-$(uname -r)  # on Debian, Ubuntu, Mint`  
`sudo yum install gcc kernel-devel               # on RHEL, CentOS, Fedora`

</code>

### Download

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/input-wacom/)
and unpack it with:

<code>

`tar xjvf input-wacom-`<version number>`.tar.bz2`  
`cd input-wacom-`<version number>

</code>

### Build / Install

From within the extracted input-wacom directory, run the following
commands to build the driver and install it where your system can find
it. The output from the **configure** step will provide the exact path
for you to use for copying.

<code>

`./configure`  
`sudo cp ./`<kernel version>`` /wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet ``  
`sudo cp ./`<kernel version>`` /wacom_w8001.ko /lib/modules/`uname -r`/kernel/drivers/input/touchscreen ``  
`sudo depmod -a`

</code>

### Module Loading

Finally, you'll need to have the module loaded by the kernel so that it
can be used by the rest of the system. The easiest way to do this is to
simply reboot; the kernel will automatically load the newly-installed
module when necessary. Alternatively, you can use the commands below to
force the running kernel to reload the module without rebooting:

<code>

`sudo modprobe -r wacom`  
`sudo modprobe -r wacom_w8001`  
`` sudo insmod /lib/modules/`uname -r`/kernel/drivers/input/tablet/wacom.ko ``  
`` sudo insmod /lib/modules/`uname -r`/kernel/drivers/input/touchscreen/wacom_w8001.ko ``

</code>

Development
-----------

**If you are developing support for new devices, please develop against
upstream kernels first.** Patches in input-wacom may not find their way
into upstream kernels. Once a device is supported upstream, feel free to
submit patches for backported support to the [linux-input mailing
list](https://patchwork.kernel.org/project/linux-input/).

The input-wacom driver is available through git for developers to hack
on. Before using it, you should install your distribution's **git**,
**autoconf**, and **automake** tools. Afterwards, you may clone the
repository as follows:

<code>

`git clone `[`git://git.code.sf.net/p/linuxwacom/input-wacom`](git://git.code.sf.net/p/linuxwacom/input-wacom)` linuxwacom-input-wacom`  
`cd linuxwacom-input-wacom`

</code>

Note that the **configure** script will not exist when building from
git. We provide a helper *autogen.sh* script that wraps executing the
autotools steps as well as executing the configure step. After running
it, you can either install the modules as normal, or use insmod with the
files directly in the build directory.

X driver
--------

Upgrading both the kernel driver and the X driver to the same version is
recommended. Please see [xf86-input-wacom](xf86-input-wacom "wikilink")
for the X driver required for X servers 1.7 and later.
