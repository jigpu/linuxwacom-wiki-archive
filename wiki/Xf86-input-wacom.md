---
title: Xf86-input-wacom
permalink: wiki/Xf86-input-wacom/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

The **xf86-input-wacom** driver is the wacom-specific X11 input driver
for the X.Org X Server version 1.7 and later (X11R7.5 or later). To work
properly, it requires that a Wacom kernel driver is installed (from
either [input-wacom](input-wacom "wikilink") or
[linuxwacom](linuxwacom "wikilink")).

Distribution Support
--------------------

Many distributions include xf86-input-wacom and a properly patched
kernel out of the box. In many cases, you can upgrade xf86-input-wacom
without needing to build anything. The following distributions ship
xf86-input-wacom:

-   Arch Linux
-   Debian Squeeze and later
-   Fedora 12 and later
-   Gentoo
-   OpenSuse 10.3 and later
-   Ubuntu 10.04 and later

In addition, some pre-built snapshots of the git repository may be
available for your distribution. If you just want to test a newer driver
than is available in your distribution's official repository without
building anything yourself, try these:

-   Ubuntu [PPA](https://launchpad.net/ubuntu/+ppas):
    <https://launchpad.net/~doctormo/+archive/wacom-plus>

Getting the Source
------------------

We recommend to use [git](/wiki/Using_Git "wikilink") to get the source
straight from the repository

    git clone git://git.code.sf.net/p/linuxwacom/xf86-input-wacom
    cd xf86-input-wacom

Alternatively, grab the latest release from the [downloads
page](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/)
and unpack it with:

    tar jxf xf86-input-wacom-<version number>.tar.bz2
    cd xf86-input-wacom-<version-number>

Configuring the Build
---------------------

We provide a helper script *autogen.sh* that wraps the
[autotools](http://en.wikipedia.org/wiki/GNU_build_system) steps. First
make sure you have the following tools installed: gcc, automake,
autoconf and make.

If you are **building from git**, then run autogen.sh:

    ./autogen.sh --prefix=/usr --libdir=/usr/lib        # on 32-bit install
    ./autogen.sh --prefix=/usr --libdir=/usr/lib64      # on 64 bit install

If you are **building from a tarball**, then run configure instead:

    ./configure --prefix=/usr --libdir=/usr/lib        # on 32-bit install
    ./configure --prefix=/usr --libdir=/usr/lib64      # on 64 bit install

The scripts check whether the [required
dependencies](dependencies "wikilink") are installed. If any are
missing, you need to install the dependencies, either from your
distribution's packaging system or from the respective project's
releases. Once installed, re-run the command.

Both prefix and libdir options cover the common Linux distributions but
there is a small chance that your distribution needs different prefixes
or libdirs. The easiest way to check is to search for a file named
*evdev\_drv.so*. If it is in */usr/lib64/xorg/modules/input* or
*/usr/lib/xorg/modules/input*, then the above commands should work for
you. If you have an X server tree outside of your system tree, adjust
the prefix accordingly.

Note that if you configured with a prefix of */usr* you will overwrite
your distribution's install. We recommend that use the same build flags
as your distribution does if you are trying to replace the
distribution-installed version of the driver with the version from git.

-   [Fedora](http://koji.fedoraproject.org/koji/packageinfo?packageID=9537):
    select the latest build for your version and look at the build.log.
    Search for the configure line and copy everything from that line.

Building the Driver
-------------------

When the step above has finished, you can build and install the driver
with the following commands.

    make
    make install # may require sudo/root

Post-Install
------------

After installing the driver, you'll need to restart the driver by
logging out. In addition, it may be necessary to configure X before you
can use your tablet. See the [configuring X](configuring_X "wikilink")
page for more information.
