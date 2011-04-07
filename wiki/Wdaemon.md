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

For static configuration, it is necessary that wdaemon is running
*before* the X server starts up.

xorg.conf.d configuration snippets
----------------------------------

wdaemon ships with a configuration examples for xorg.conf.d (X server
1.8 and later). The approach these examples use is:

1.  disable the wacom driver for all physical wacom tablets
2.  enable the wacom driver for all wdaemon devices

The example xorg.conf.d snippet:

    Section "InputClass"
            Identifier "wdaemon ignore all devices"
            MatchProduct "Wacom|WACOM"
            MatchDevicePath "/dev/input/event*"
            Option "Ignore" "on"
    EndSection

    Section "InputClass"
            Identifier "wdaemon unignore uinput devices"
            # the wdaemon udev rules tag the device
            MatchTag "wdaemon-device"
            Driver "wacom"
            Option "Ignore" "off"
    EndSection

As you can see, we use the udev tag assigned by the rules file to
disable Ignore, which causes the device to be added again.

For finer-grained matching (e.g. on a specific tablet), adjust the
MatchProduct line in the first part accordingly. Once adjusted, the X
server will hotplug all devices but the matched ones, hence providing
both real hotplugging and wdaemon-style hotplugging.

HAL configuration
-----------------

wdaemon ships with a configuration example for HAL configuration (X
Servers 1.5 to 1.7). The approach used is the same as for the
[xorg.conf.d snippet](#xorg.conf.d_configuration_snippets "wikilink")
above.

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <deviceinfo version="0.2">
      <device>
        <match key="info.category" contains="input">
          <match key="info.product" contains_outof="Wacom;WALTOP;WACOM">
             <remove key="input.x11_driver" />
          </match>
          <!-- enable wacom driver for all wdaemon devices -->
          <match key="info.product" contains="wdaemon">
            <merge key="input.x11_driver" type="string">wacom</merge>
          </match>
        </match>
      </device>
    </deviceinfo>            

Here we rely on wdaemon to prefix all devices names with *"wdaemon"* (we
can't match on the udev tag with HAL). Again, for finer-grained matching
adjust the *info.product* line above. Once adjusted, save this file in
*/etc/fdi/hal/policy* and restart HAL. It is recommended that you check
for correct configuration with `lshal`. If correctly configured, the
`input.x11_driver` is only set to wacom on the wdaemon devices, but not
on the physical devices (wdaemon should be running to see this).

A note on xorg.conf.d and HAL configuration
-------------------------------------------

In the case of xorg.conf.d or HAL configuration, we still use the
hotplugging facilities of the X server. wdaemon does not need to be
running at server startup. The X server will hotplug the virtual devices
when wdaemon is started up. The devices added will use a
*/dev/input/eventX* device path, hence only the *60-wacom.rules* udev
rule is required for such a configuration. No uinput symlinks are
required.

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

Emulating unknown and new devices
=================================

wdaemon can emulate tablets without the physical tablet being present.
To do this, wdaemon must know the properties of each device. Information
about most tablets is built into wdaemon but newer devices may not be
supported. If wdaemon needs to emulate a new device, it must first
gather a device description from this device. To get such a description,
use the *-x* flag (for "extract").

` wdaemon -x newdevice.desc -p /dev/input/event12`

This will save the device description of the device on
*/dev/input/event12* in the file *newdevice.desc*. To re-use this
description, simply load the device description.

` wdaemon -l newdevice.desc -p /dev/input/event12`

This command will re-create the device described by the file and link it
to the device at */dev/input/event12*. This line may also be specified
in the configuration file as

` description = path/to/newfile.desc,/dev/input/event12`

X.Org configuration is not affected if wdaemon does not natively know a
device. The above guidelines still apply.
