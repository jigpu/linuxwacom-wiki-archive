---
title: Wdaemon
permalink: wiki/Wdaemon/
layout: wiki
tags:
 - HOWTO
---

[wdaemon](wdaemon "wikilink") is a daemon to hide physical tablet
unplugging. When it was originally written, the X server did not have
input device hotplugging support. Input devices added at runtime could
thus not be used. wdaemon worked around this by providing a permanent
virtual kernel device that looked exactly like the physical tablet to
userspace. The X server was then configured to use the wdaemon device.
Whenever a physical tablet was plugged in, wdaemon would then forward
the events from this device through the virtual device.

Now the X server *does* support input device hotplugging. From a
technical point of view, wdaemon works exactly as before. From a
practical perspective however it works the other way round - it
*disables* device hotplugging. A device that would otherwise appear and
disappear as X input device now looks like a permanent device to X.
Thus, run-time settings applied to this device remain permanent.

Note that the virtual device created by wdaemon will still be hotplugged
by the X server (if so configured), hence wdaemon provides a two-layer
hotplug abstraction.

Getting wdaemon
===============

The git repository is available at

` `[`git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/wdaemon`](git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/wdaemon)

wdaemon can be built and installed with the usual

`  ./autogen.sh --prefix=/usr`  
`  make && make install`

Configuration
=============

wdaemon requires configuration of udev and X.Org for proper functioning.

udev configuration
------------------

wdaemon comes with udev rules files. These should be installed in the
udev rules.d directory (usually /lib/udev/rules.d). Three rules files
are provided:

-   **60-wacom.rules**: this rule creates a symlink for each tablet
    model in /dev/input/wacom-tablets/. Example:
    /dev/input/wacom-tablets/intuos4-6x9.
-   **61-uinput-wacom.rules**: this rule runs a script on the event
    devices and if the device is a uinput device created by wdaemon, it
    creates a symlink in
    /dev/input/uinput-devices/uinput-wacom-...-tablet. It also applies a
    udev tag named "wdaemon-device"
-   **61-uinput-stddev.rules**: same as 61-uinput-wacom.rules but for
    non-tablet devices. You can usually ignore this rule

Kernel event device numbers are not guaranteed to stay the same when the
device is removed. Thus a symlink can help greatly with permanent
configurations to always point to the same device.

static xorg.conf configuration
------------------------------

To add a wdaemon device to a static xorg.conf, use the uinput link as
device.

    Section "InputDevice"
        Identifier "wdaemon device"
        Driver "wacom"
        Option "Device" "/dev/input/uinput-devices/uinput-wacom-..."
        # add other options here
    EndSection

Running wdaemon
===============

In the simplest case, you run wdaemon with the numeric type of the
device and the device path to monitor. Note that wdaemon usually needs
to run as root for the appropriate permissions.

` wdaemon -t 56 -p /dev/input/event12 `

or, to use the udev symlinks instead:

` wdaemon -t 52 -p /dev/input/wacom-tablets/intuos4-6x9`

wdaemon also supports a configuration file format. The above command,
when used in a configuration file would be

` device = 56,/dev/input/wacom-tablets/intuso4-6x9`

More lines may be added for additional tablets. wdaemon can then be
started with the configuration file (and backkgrounded)

` wdaemon -f -c path/to/config.file`
