---
title: Xf86-input-wacom
permalink: wiki/Xf86-input-wacom/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

The **xf86-input-wacom** driver provides desktop support for Wacom
tablets, touchscreens, and similar hardware. It is responsible for
relaying events from the kernel to the X server where applications like
GIMP, Krita, and Xournal can make use of them. Additionally, it provides
services like palm rejection that improve the experience of using a
tablet.

Get the Drivers
---------------

The easiest (and recomended) way to get support for Wacom hardware is to
install the packages provided by your distribution. Our drivers are
provided out of the box with most distributions, but can be updated or
installed with the following commands:

| Fedora, Red Hat, Mageia | `$ sudo -s 'yum makecache && yum install xorg-x11-drv-wacom'`            |
|-------------------------|--------------------------------------------------------------------------|
| Ubuntu, Mint, Debian    | `$ sudo -s 'apt-get update && apt-get install xserver-xorg-input-wacom'` |
| SUSE                    | `$ sudo -s 'zypper update && zypper install xf86-input-wacom'`           |
| Arch Linux              | `$ sudo -s 'pacman -Sy && pacman -S xf86-input-wacom'`                   |
| Gentoo                  | `$ sudo -s 'emerge --sync && emerge xf86-input-wacom'`                   |

Building from Source
--------------------

If the drivers provided by your distribution are out of date, you can
also compile and install the driver from source. Before proceeding,
you'll need to first install some required software:

| Fedora, Red Hat, Mageia | `$ sudo yum install gcc xorg-x11-util-macros xorg-x11-server-devel libXext-devel libXi-devel libXrandr-devel libXinerama-devel libudev-devel` |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| Ubuntu, Mint, Debian    | `$ sudo apt-get install xutils-dev libtool xserver-xorg-dev libx11-dev libxi-dev libxrandr-dev libxinerama-dev libudev-dev`                   |

    $ wget https://sourceforge.net/projects/linuxwacom/files/latest/download -O xf86-input-wacom.tar.bz2
    $ tar xvjf xf86-input-wacom.tar.bz2
    $ cd xf86-input-wacom-*
    $ ./configure --prefix=/usr
    $ make
    $ sudo make install

Developers also have the option of cloning our git repository from
[`git://git.code.sf.net/p/linuxwacom/xf86-input-wacom`](git://git.code.sf.net/p/linuxwacom/xf86-input-wacom).
In addition to the dependencies listed above, the 'automake',
'autoconf', and 'git' packages for your distribution will need to be
installed. As with other autotools-based projects, the `./automake.sh`
command should be run after cloning to create the Makefile.

Post-Install
------------

After installing the driver, you'll need to restart the driver by
logging out. In addition, it may be necessary to configure X before you
can use your tablet. See the [configuring X](configuring_X "wikilink")
page for more information.
