---
title: Configuring X
permalink: wiki/Configuring_X/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

A guide to configuring your system so that X can detect the devices.

Hotplugging setup with udev
---------------------------

X Servers 1.8 and later use udev as device enumeration backend on Linux.
Note that ideally no X-related configuration is stored in udev itself,
it is simply used for getting hotplug notifications and a list of
available devices. The devices visible to the X server include all
devices with the udev property *ID\_INPUT* set to 1 (see *udevadm info
--export-bd*).

Actual configuration is performed through [xorg.conf.d
snippets](http://who-t.blogspot.com/2010/01/new-configuration-world-order.html).
The xf86-input-wacom git tree ships an example configuration file in
[*/conf/50-wacom.conf*](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/xf86-input-wacom;a=blob;f=conf/50-wacom.conf;h=b1742bceb0f0abb033306adf68d4854ef438b288;hb=HEAD).
Drop this into your local */etc/X11/xorg.conf.d* directory and you're
good to go.

Hotplugging setup with HAL
--------------------------

**X Servers 1.8 and later do not use HAL by default see [\#Hotplugging
setup with udev](#Hotplugging_setup_with_udev "wikilink") instead**

Ideally, you should be using the X Server's hotplugging features instead
of manual configuration. This allows you to (un)plug the device at
runtime. The driver provides a wacom.fdi file that is by default
installed into HAL's policy directory
(<i>/usr/share/hal/fdi/policy/20thirdparty</i>). Once installed, you
need to restart HAL and verify that the right driver will be loaded.

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

Manual setup in the xorg.conf
-----------------------------

The usb symlinks *"/dev/input/wacom" & "/dev/input/wacom-touch"* will
only work if you have the correct wacom.rules file in your
distribution's appropriate udev rules.d directory. See "Updated wacom.c"
in [USB Kernel driver](/wiki/USB_Kernel_driver "wikilink").

NOTE: *Option "ForceDevice" "ISDV4"* was deprecated starting with
xf86-input-wacom-0.10.8. You can remove it from your xorg.conf sections
if using 0.10.8 or later.
