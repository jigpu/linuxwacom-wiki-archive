---
title: Xorg.conf
permalink: wiki/Xorg.conf/
layout: wiki
tags:
 - HOWTO
---

X Servers 1.5 or later (released Sep 3, 2008
[1](http://lists.x.org/archives/xorg/2008-September/038187.html)) will
by default hotplug input devices if they are known to the server's
configuration backend
([udev](http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev.html)
in 1.8 and later, or [HAL](http://hal.freedesktop.org)). Manually
configuring your tablets by adding them into the `xorg.conf` file is not
reccomended if your X server supports hotplugging, since manually
configured tablets loose the ability to be hotplugged. Older X servers,
or special-case scenarios not well-handled by hotplugging should follow
this guide.

Example xorg.conf
-----------------

Each device type (stylus, erasor, etc.) needs to be added as a separate
InputDevice section and will appear as its own input device in X.

The usb symlinks `/dev/input/wacom` and `/dev/input/wacom-touch` will
only work if you have the correct wacom.rules file in your
distribution's appropriate udev rules.d directory. See "Updated wacom.c"
in [USB Kernel driver](/wiki/USB_Kernel_driver "wikilink").

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
