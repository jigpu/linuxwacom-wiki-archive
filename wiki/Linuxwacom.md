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

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/linuxwacom/)
and unpack it with:

    tar jxf linuxwacom-<version number>.tar.bz2
    cd linuxwacom-<version number>

Alternatively, pre-release code is available at the [linuxwacom git
repository](https://sourceforge.net/p/linuxwacom/code/ci/master/tree):

    git clone git://git.code.sf.net/p/linuxwacom/code linuxwacom
    cd linuxwacom
    ./bootstrap

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

Building the code with the above instructions will build the X driver if
it detects you are using a sufficiently old X server. After installing
the driver, you'll need to restart X by logging out. In addition, it may
be necessary to configure X before you can use your tablet. See the
[configuring X](configuring_X "wikilink") page for more information.
