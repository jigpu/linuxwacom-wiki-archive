---
title: Configuring X
permalink: wiki/Configuring_X/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

A guide to configuring your system so that X can detect the devices.

Hotplugging Setup
-----------------

Ideally, you should be using the X server's hotplugging features instead
of manual configuration. In contrast to a manually configured xorg.conf
file (which requires devices devices to be plugged in when the X server
starts), hotplugging allows you to freely plug and unplug your tablets
at any time. When a device is plugged in, the hotplug configuration is
read to determine the correct settings to apply. By editing the
configuration, you can modify the default settings your tablet starts
with.

Note that any run-time configuration settings set through
[xsetwacom](xsetwacom "wikilink") or a control panel will be lost when a
hotplugged tablet is unplugged. The [wdaemon](wdaemon "wikilink")
utility can be helpful in this case.

### Hotplugging With udev

X Servers 1.8 and later use udev as the device enumeration backend on
Linux. Unlike the HAL backend, **no X-related configuration is stored in
udev itself** -- udev simply alerts X of new devices which have the udev
property *ID\_INPUT* set to 1 (see `udevadm info --export-db`). The X
server scans several directories for .conf files on startup and uses
their contents to setup devices udev notifies it of:

-   */usr/share/X11/xorg.conf.d*
    -   Files here are the shipped by the distribution and may be
        overwritten at update time. It is not recommended to place any
        user-specific configuration files here.
-   */etc/X11/xorg.conf.d*
    -   Files here are user-specific configurations. If there is no
        user-specific configuration, this directory may not exist -
        simply create it and store your configurations here.
    -   Files here are read after the distribution-specific patches, so
        user-specific configuration always overrides the default
        configurations.

More than one configuration snippet may apply to any device.
Configuration settings are merged, but if the same setting is specified
multiple times, only the last one is used. Usually, your distribution
ships with a number of .conf files that will pick the right driver for
your device. Thus, if you want to configure a particular device through
xorg.conf.d snippets, you can usually skip the driver assignment - it's
already done for you.

The xf86-input-wacom git tree ships an example configuration file in
[*/conf/50-wacom.conf*](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/xf86-input-wacom;a=blob;f=conf/50-wacom.conf;h=b1742bceb0f0abb033306adf68d4854ef438b288;hb=HEAD).
Drop this into your local */etc/X11/xorg.conf.d* directory and you're
good to go.

For specific details on how to customize the your configuration, see the
[xorg.conf.d](xorg.conf.d "wikilink") page.

### Hotplugging With HAL

X servers prior to 1.8 which support hotplugging use HAL by default. The
driver provides a `wacom.fdi` file that is by default installed into
HAL's policy directory (*/usr/share/hal/fdi/policy/20thirdparty*). Once
installed, you need to restart HAL and verify that the right driver will
be loaded.

    $> lshal | grep wacom
      info.linux.driver = 'wacom'  (string)
      input.x11_driver = 'wacom'  (string)

The second line shows that there is at least one device that will be
using the wacom driver. If this line is missing, the fdi is not
installed in the correct place, HAL hasn't been restarted, the device is
not plugged in or some other error occured. The full lshal output for
your tablet device will usually look something like this:

    udi = '/org/freedesktop/Hal/devices/usb_device_56a_b9_noserial_if0_logicaldev_input'
      info.capabilities = {'input', 'input.tablet'} (string list)
      info.category = 'input'  (string)
      info.parent = '/org/freedesktop/Hal/devices/usb_device_56a_b9_noserial_if0'  (string)
      info.product = 'Wacom Intuos4 6x9'  (string)
      info.subsystem = 'input'  (string)
      info.udi = '/org/freedesktop/Hal/devices/usb_device_56a_b9_noserial_if0_logicaldev_input'  (string)
      input.device = '/dev/input/event10'  (string)
      input.originating_device = '/org/freedesktop/Hal/devices/usb_device_56a_b9_noserial_if0'  (string)
      input.product = 'Wacom Intuos4 6x9'  (string)
      input.x11_driver = 'wacom'  (string)
      linux.device_file = '/dev/input/event10'  (string)
      linux.hotplug_type = 2  (0x2)  (int)
      linux.subsystem = 'input'  (string)
      linux.sysfs_path = '/sys/devices/pci0000:00/0000:00:1a.1/usb4/4-1/4-1:1.0/input/input47/event10'  (string)

Static xorg.conf Setup
----------------------

X Servers 1.5 or later (released Sep 3, 2008
[1](http://lists.x.org/archives/xorg/2008-September/038187.html)) will
by default hotplug input devices if they are known to the server's
configuration backend
([udev](http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev.html)
in 1.8 and later, or [HAL](http://hal.freedesktop.org)). Manually
configuring your tablets by adding them into the *xorg.conf* file is not
reccomended if your X server supports hotplugging, since manually
configured tablets loose the ability to be hotplugged. Older X servers,
or special-case scenarios not well-handled by hotplugging should follow
this guide.

Each device type (stylus, erasor, etc.) needs to be added as a separate
InputDevice section and will appear as its own input device in X.

The usb symlinks `/dev/input/wacom` and `/dev/input/wacom-touch` will
only work if you have the correct wacom.rules file in your
distribution's appropriate udev rules.d directory. See [fixed device
files with udev](fixed_device_files_with_udev "wikilink").

**NOTE: `Option "ForceDevice" "ISDV4"` was deprecated starting with
xf86-input-wacom-0.10.8.** You can remove it from your xorg.conf
sections if using 0.10.8 or later.

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "stylus"
      Option        "Device"        "/dev/ttyS0"              # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"        # USB ONLY
      Option        "Type"          "stylus"
      Option        "USB"           "on"                      # USB ONLY
      Option        "ForceDevice"   "ISDV4"                   # Serial Tablet PC ONLY
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "eraser"
      Option        "Device"        "/dev/ttyS0"              # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"        # USB ONLY
      Option        "Type"          "eraser"
      Option        "USB"           "on"                      # USB ONLY
      Option        "ForceDevice"   "ISDV4"                   # Serial Tablet PC ONLY
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "cursor"                                  # Wacom tablet mouse
      Option        "Device"        "/dev/ttyS0"              # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"        # USB ONLY
      Option        "Type"          "cursor"
      Option        "USB"           "on"                      # USB ONLY
      Option        "ForceDevice"   "ISDV4"                   # Serial Tablet PC ONLY
    EndSection

    # This section is for Tablets & TabletPCs that support touch
    Section "InputDevice"
      Driver        "wacom"
      Identifier    "touch"
      Option        "Device"        "/dev/ttyS0"              # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom-touch"  # USB ONLY
      Option        "Type"          "touch"
      Option        "ForceDevice"   "ISDV4"                   # Serial Tablet PC ONLY
      Option        "USB"           "on"                      # USB ONLY
    EndSection

    # This section is for Intuos3, Intuos4, CintiqV5, Graphire4, Bamboo, or BambooPT
    Section "InputDevice"
      Driver        "wacom"
      Identifier    "pad"                                     # Tablet buttons
      Option        "Device"        "/dev/ttyS0"              # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"        # USB ONLY
      Option        "Type"          "pad"
      Option        "USB"           "on"                      # USB ONLY
    EndSection

Once the InputDevice sections are present, they need to be referenced
from the ServerLayout section to take effect. An example layout section
may look like this:

    Section "ServerLayout"
      Identifier    "X.org Configured"
      InputDevice   "stylus"
      InputDevice   "eraser"
      InputDevice   "cursor"     # For non-LCD tablets only
      InputDevice   "touch"      # For TabletPCs and BambooPTs that support touch
      InputDevice   "pad"        # For Intuos3/Intuos4/CintiqV5/Graphire4/Bamboo/BambooPT
    EndSection
