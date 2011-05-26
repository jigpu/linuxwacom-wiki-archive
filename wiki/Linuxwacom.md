---
title: Linuxwacom
permalink: wiki/Linuxwacom/
layout: wiki
---

**linuxwacom** is a legacy driver package containing both an X driver
and Linux kernel driver. It supports X servers from 1.4 to 1.6 and
provides backports for Linux kernels from 2.6.16 to 2.6.25. **If you run
a reasonably modern desktop distribution, you do not need this
package.** All linuxwacom-related pages can be found in this wiki's
[linuxwacom category](/wiki/Category%3ALinuxwacom "wikilink").

Getting the Source
------------------

linuxwacom provides two types of packages - stable and development
versions. Please download the latest release from the [sourceforge
download section](https://sourceforge.net/projects/linuxwacom/files/)
and unpack it with:

    tar jxf linuxwacom-<version number>.tar.bz2
    cd linuxwacom-<version number>

Building the code
-----------------

Once you have the package downloaded, you can build it with the commands
below. A more exhaustive description of the build process is avaiable in
[building linuxwacom](building_linuxwacom "wikilink").

The `--enable-wacom` flag enables the build of the wacom kernel driver;
if you do not want to build it, simply leave out this flag. The prefix
of /usr is correct on most distributions (unfortunately, the default is
still /usr/local which isn't used widely anymore).

    ./configure --prefix=/usr --enable-wacom
    make
    make install   # may require sudo/root

linuxwacom X Driver
-------------------

The linuxwacom tarball includes an X driver for X servers up to
including the 1.6.x series. For newer X servers, please use
[xf86-input-wacom](xf86-input-wacom "wikilink") instead: the linuxwacom
X driver will not build. Note that if you run a kernel that requires
linuxwacom backports, you may still do so. Install the kernel patches
provided in the tarball and then the
[xf86-input-wacom](xf86-input-wacom "wikilink") X driver.
