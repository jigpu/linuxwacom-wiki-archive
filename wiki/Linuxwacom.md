---
title: Linuxwacom
permalink: wiki/Linuxwacom/
layout: wiki
---

linuxwacom is a combined kernel + X driver package for X servers up to
including 1.6.

Download
--------

Please download the latest release from the [sourceforge download
section](https://sourceforge.net/projects/linuxwacom/files/). linuxwacom
provides two types of packages - stable and development versions.

Building the code
-----------------

In the following, you need to replace the version number with the one
from your downloaded tarball.

    tar jxf linuxwacom-0.8.4-4.tar.bz2
    cd linuxwacom-0.8.4-4
    ./configure --prefix=/usr --enable-wacom
    make && sudo make install

--enable-wacom enables the build of the wacom kernel driver. If you do
not want to build it, simply leave out this flag. The prefix of /usr is
correct on most distributions (unfortunately, the default is still
/usr/local which isn't used widely anymore).
