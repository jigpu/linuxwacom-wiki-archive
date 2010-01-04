---
title: Xf86-input-wacom
permalink: wiki/Xf86-input-wacom/
layout: wiki
---

The xf86-input-wacom driver is the wacom-specific X11 input driver for
the X.Org X Server version 1.7 and later (X11R7.5 or later).

### Installing from git

    git clone git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom
    cd xf86-input-wacom
    ./autogen.sh --prefix=/usr
    make && make install

This gets the latest version from git and installs it into the given
prefix. In most cases, the prefix of /usr is the right one. If you're on
a 64-bit machine, configure with --libdir=/usr/lib64. If unsure, search
for evdev\_drv.so. If it is in /usr/lib/xorg/modules/input, then the
prefix of /usr is enough. If it is in /usr/lib64/xorg/modules/input,
then you need to specify --libdir=/usr/lib64.

### Testing

Once installed, you need to restart the X server. Either hit
Ctrl+Alt+Backspace (if enabled), log out or reboot, whichever you
prefer. If the driver is working successfully, the log should show an
entry similar to this one:

    (II) LoadModule: "wacom"
    (II) Loading /opt/xorg/lib/xorg/modules/input/wacom_drv.so
    (II) Module wacom: vendor="X.Org Foundation"
            compiled for 1.7.99.3, module version = 0.10.3
            Module class: X.Org XInput Driver
            ABI class: X.Org XInput driver, version 9.0

This snippet shows a wacom version of 0.10.3 compiled against a server
version 1.7.99.3. In addition, you'll find lines in the log listing the
actual device found and its configuration.

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
