---
title: Xorg.conf
permalink: wiki/Xorg.conf/
layout: wiki
---

Each device type (stylus, erasor, etc.) needs to be added as a separate
InputDevice section and will appear as its own input device in X.

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "stylus"
      Option        "Device"        "/dev/ttyS0"          # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "stylus"
      Option        "USB"           "on"                  # USB ONLY
      Option        "ForceDevice"   "ISDV4"               # Serial Tablet PC ONLY
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "eraser"
      Option        "Device"        "/dev/ttyS0"          # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "eraser"
      Option        "USB"           "on"                  # USB ONLY
      Option        "ForceDevice"   "ISDV4"               # Serial Tablet PC ONLY
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "cursor"
      Option        "Device"        "/dev/ttyS0"          # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "cursor"
      Option        "USB"           "on"                  # USB ONLY
      Option        "ForceDevice"   "ISDV4"               # Serial Tablet PC ONLY
    EndSection

    # This section is for Intuos3, CintiqV5, Graphire4, or Bamboo
    Section "InputDevice"
      Driver        "wacom"
      Identifier    "pad"
      Option        "Device"        "/dev/ttyS0"          # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "pad"
      Option        "USB"           "on"                  # USB ONLY
    EndSection

    # This section is for the TabletPC that supports touch
    Section "InputDevice"
      Driver        "wacom"
      Identifier    "touch"
      Option        "Device"        "/dev/ttyS0"          # SERIAL ONLY
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "touch"
      Option        "ForceDevice"   "ISDV4"               # Serial Tablet PC ONLY
      Option        "USB"           "on"                  # USB ONLY
    EndSection

Once the InputDevice sections are present, they need to be referenced
from the ServerLayout section to take effect. An example layout section
may look like this:

    Section "ServerLayout"
            Identifier     "Default Layout"
            Screen 0 "Screen0"   0 0
            InputDevice    "Mouse0"    "CorePointer"
            InputDevice    "Keyboard0" "CoreKeyboard"
            InputDevice    "stylus"    "SendCoreEvents"
            InputDevice    "eraser"    "SendCoreEvents"
            InputDevice    "cursor"    "SendCoreEvents"    # For non-LCD tablets only
        InputDevice    "touch"     "SendCoreEvents"    # Only a few TabletPCs support this type
            InputDevice    "pad"   # For Intuos3/CintiqV5/Graphire4/Bamboo tablets
    EndSection
