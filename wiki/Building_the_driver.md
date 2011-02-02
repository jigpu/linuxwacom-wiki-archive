---
title: Building the driver
permalink: wiki/Building_the_driver/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

This page lists instructions for building the
[xf86-input-wacom](xf86-input-wacom "wikilink") driver. For
[linuxwacom](linuxwacom "wikilink")-specific instructions, click
[here](/wiki/Building_linuxwacom "wikilink")

Getting the source
------------------

We recommend to use [git](/wiki/Using_Git "wikilink") to get the source
straight from the repository

`   git clone `[`git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom`](git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom)  
`   cd xf86-input-wacom`

Alternatively, grab the latest release from the [downloads
page](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/)
and unpack it with

`   tar jxf xf86-input-wacom-`<version number>`.tar.bz2`  
`   cd xf86-input-wacom-`<version-number>

Configuring the build
---------------------

We provide a helper script *autogen.sh* that wraps the
[autotools](http://en.wikipedia.org/wiki/GNU_build_system) steps. First
make sure you have the following tools installed: gcc, automake,
autoconf and make. Then run autogen.sh:

`   ./autogen.sh --prefix=/usr --libdir=/usr/lib        # on 32-bit install`  
`   ./autogen.sh --prefix=/usr --libdir=/usr/lib64      # on 64 bit install`

Both commands cover the common Linux distributions but there is a small
chance that your distribution needs different prefixes or libdirs. The
easiest way to check is to search for a file named *evdev\_drv.so*. If
it is in */usr/lib64/xorg/modules/input* or
*/usr/lib/xorg/modules/input*, then the above commands will work for
you.

*autogen.sh* calls configure which will check whether the required
dependencies are installed. If any are missing, you need to install the
dependencies, either from your distribution's packaging system or from
the respective project's releases. Once installed, re-run *autogen.sh*
as above.

Dependencies include (this list may change as the driver is update):

-   the X server SDK
-   libX11, libXi and libXrandr development files

Building
--------

`   make`  
`   make install # may require sudo/root`

Note that if you configured with a prefix of */usr* you will overwrite
your distribution's install. If you have an X server tree outside of
your system tree, adjust the prefix accordingly. We recommend that use
the same build flags as your distribution does if you are trying to
replace the distribution-installed version of the driver with the
version from git.

-   [Fedora](http://koji.fedoraproject.org/koji/packageinfo?packageID=9537):
    select the latest build for your version and look at the build.log.
    Search for the configure line and copy everything from that line.

Running the new driver
----------------------

Once installed, restart your X server by logging out. When the server
comes back up, it will use the new driver. You can verify this by
checking the version number in the */var/log/Xorg.0.log*.

    (II) LoadModule: "wacom"
    (II) Loading /opt/xorg/lib/xorg/modules/input/wacom_drv.so
    (II) Module wacom: vendor="X.Org Foundation"
            compiled for 1.7.99.3, module version = 0.10.3
            Module class: X.Org XInput Driver
            ABI class: X.Org XInput driver, version 9.0

The *module version* number will show the current version number of the
driver. This snippet shows a wacom version of 0.10.3 compiled against a
server version 1.7.99.3. In addition, you'll find lines in the log
listing the actual device found and its configuration.

    (II) config/hal: Adding input device Wacom Intuos4 6x9
    (**) Option "Device" "/dev/input/event13"
    (II) Wacom Intuos4 6x9: type not specified, assuming 'stylus'.
    (II) Wacom Intuos4 6x9: other types will be automatically added.
    (**) Wacom Intuos4 6x9: always reports core events
    (II) Wacom Intuos4 6x9: hotplugging dependent devices.
    (**) Option "Device" "/dev/input/event13"
    (**) Wacom Intuos4 6x9 eraser: always reports core events
    (II) XINPUT: Adding extended input device "Wacom Intuos4 6x9 eraser" (type: ERASER)
    (--) Wacom Intuos4 6x9 eraser: using pressure threshold of 245 for button 1
    (--) Wacom Intuos4 6x9 eraser: Wacom USB Intuos4 tablet speed=38400 maxX=44704 maxY=27940 maxZ=2047 resX=5080 resY=5080  tilt=enabled
    (--) Wacom Intuos4 6x9 eraser: top X=0 top Y=0 bottom X=44704 bottom Y=27940 resol X=5080 resol Y=5080
    (**) Option "Device" "/dev/input/event13"
    (**) Wacom Intuos4 6x9 cursor: always reports core events
    (II) XINPUT: Adding extended input device "Wacom Intuos4 6x9 cursor" (type: CURSOR)
    (--) Wacom Intuos4 6x9 cursor: top X=0 top Y=0 bottom X=44704 bottom Y=27940 resol X=5080 resol Y=5080
    (**) Option "Device" "/dev/input/event13"
    (**) Wacom Intuos4 6x9 pad: always reports core events
    (II) XINPUT: Adding extended input device "Wacom Intuos4 6x9 pad" (type: PAD)
    (--) Wacom Intuos4 6x9 pad: top X=0 top Y=0 bottom X=44704 bottom Y=27940 resol X=5080 resol Y=5080
    (II) Wacom Intuos4 6x9: hotplugging completed.

The actual text of the log depends on the device but it looks similar.