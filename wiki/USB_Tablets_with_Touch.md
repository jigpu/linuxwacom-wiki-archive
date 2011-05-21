---
title: USB Tablets with Touch
permalink: wiki/USB_Tablets_with_Touch/
layout: wiki
tags:
 - HOWTO
---

USB tablets with touch include USB tablet PCs and the Bamboo Pen & Touch
series of tablets. Because their stylus/eraser & touch or touch/pad are
exported as two separate kernel devices they have some idiosyncrasies
we'll explore briefly. BambooPT examples will be shown because except
for the pad (tablet buttons) they also apply to a USB tablet PC.

### X servers 1.8 & 1.9

Usually only one snippet is enough as you cannot configure dependent
devices. The USB devices with touch appear to be an exception, but
that's only because their touch is a separate device.

The following example shows a BambooPT 52-wacom-options.conf in
/etc/X11/xorg.conf.d with two snippets for the two different devices. A
more detailed discussion of 52-wacom-options.conf use is given in
[xorg.conf.d](xorg.conf.d "wikilink"). Notice the *Driver "wacom"* line
is not needed and a suitable *Identifier* is chosen. Identify each input
tool's device name with [xsetwacom list](xsetwacom "wikilink"). This
will let you determine what keyword(s) will be useful for a match. In
this case the device "Pen" has stylus as the parent and eraser as the
dependent tool. For "Finger" touch is the parent and pad the dependent
tool. Separate Options valid for each device are applied.

    Section "InputClass"
        Identifier "Wacom Bamboo Presscurve and Area configurations"
            # Match all Wacom input tools that have "BambooFun 2FG 4x5 Pen" in the "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Pen"
        MatchDevicePath "/dev/input/event*"

            # Apply a custom configuration options. Any option will also apply to
            # all dependent devices, if applicable. So e.g. the pressure curve will
            # be available on the stylus and the eraser (but not on the pad).
        Option "PressCurve" "0 10 90 100"
        Option "TopX" "0"
        Option "TopY" "0"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom Bamboo Finger Area configuration"
            # Match all Wacom input tools that have "BambooFun 2FG 4x5 Finger" in the "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Finger"
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "TopX" "0"
        Option "TopY" "0"
        Option "BottomX" "480"
        Option "BottomY" "320"
    EndSection

The Wacom Tablet Area settings for touch have no effect of course on the
pad.

### X server 1.10

Since X server 1.10 supports dependent device configuration we see it is
no longer really relevant that there are two devices. The distinction
essentially disappears since specifying unique input tool keywords will
pick up both device's input tools just like multiple input tools on one
device. And because the kernel is exporting the parent devices as Pen
and Finger, the parent device matching limitation of X server 1.10
(brought about by the xf86-input-wacom driver appending the type after
the match has been applied), can apparently be avoided. Provided the
match is made to Pen and Finger rather than stylus and touch.

    Section "InputClass"
        Identifier "Wacom Bamboo stylus options"
            MatchDriver "wacom"
            MatchProduct "Pen"

            # Apply custom Options to this device below.
        Option "RawSample" "20"
        Option "PressCurve" "0,10,90,100"
        Option "TopX" "-10"
        Option "TopY" "-20"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom Bamboo eraser options"
            MatchDriver "wacom"
            MatchProduct "eraser"

            # Apply custom Options to this device below.
        Option "RawSample" "20"
        Option "PressCurve" "5,0,100,95"
        Option "TopX" "-10"
        Option "TopY" "-20"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom Bamboo touch options"
            MatchDriver "wacom"
            MatchProduct "Finger"

            # Apply custom Options to this device below.
        Option "ScrollDistance" "18"
        Option "TapTime" "220"
    EndSection

    Section "InputClass"
        Identifier "Wacom Bamboo pad options"
            MatchDriver "wacom"
            MatchProduct "pad"

            # Apply custom Options to this device below.
        Option "Button1" "1"
        Option "Button2" "3"
        Option "Button3" "2"
        Option "Button4" "0"
    EndSection

### xorg.conf

See [Manual setup in the
xorg.conf](/wiki/Configuring_X#Manual_setup_in_the_xorg.conf "wikilink") for
further explanation.

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "stylus"
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "stylus"
      Option        "USB"           "on"                  # USB ONLY
      Option        "Button2"       "2"  # make first button a middle click
      Option        "Button3"       "3"  # make second button a R click
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "eraser"
      Option        "Device"        "/dev/input/wacom"    # USB ONLY
      Option        "Type"          "eraser"
      Option        "USB"           "on"                  # USB ONLY
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "touch"
      Option        "Device"        "/dev/input/wacom-touch"   # USB ONLY
      Option        "Type"          "touch"
      Option        "USB"           "on"                  # USB ONLY
    EndSection

    Section "InputDevice"
      Driver        "wacom"
      Identifier    "pad"                                 # Tablet buttons
      Option        "Device"        "/dev/input/wacom-touch"   # USB ONLY
      Option        "Type"          "pad"
      Option        "USB"           "on"                  # USB ONLY
    EndSection

    Section "ServerLayout"
      Identifier    "X.org Configured"
      InputDevice   "stylus"
      InputDevice   "eraser"
      InputDevice   "touch"         # For TabletPCs and BambooPTs that support touch
      InputDevice   "pad"   # For Intuos3/Intuos4/CintiqV5/Graphire4/Bamboo/BambooPT
    EndSection

Notice the BambooPT pad requires the /dev/input/wacom-touch symlink as
it is a dependent device of the parent device touch.

### Profiles for USB Touch

A *Profile* contains the settings you prefer for a specific program. See
[Profiles](/wiki/Tablet_Configuration#Profiles "wikilink") for more detail.
Let's write a Gimp profile for a Bamboo Pen and Touch tablet to explore
the differences. Because the Bamboo P&T is seen by the system as 2
devices, stylus/eraser & touch/pad, it needs two DEVICE variables unlike
the Bamboo script in *Profiles*. Or you could use vanilla xsetwacom
commands as in the [Sample Runtime
Script](/wiki/Tablet_Configuration#Sample_Runtime_Script "wikilink"). Here you
want to turn touch off, because you don't want it on in Gimp and it also
frees up a precious button.

    #!/bin/bash

    DEVICE="Wacom BambooFun 2FG 4x5 Pen"
    DEVTWO="Wacom BambooFun 2FG 4x5 Finger"

    STYLUS="$DEVICE stylus"
    ERASER="$DEVICE eraser"
    TOUCH="$DEVTWO touch"
    PAD="$DEVTWO pad"

    ## Stylus

    ## Eraser

    ## Touch (2FGT)
    xsetwacom set "$TOUCH" Touch off

    ## Pad
    xsetwacom set "$PAD" Button 1 "key shift ctrl n"  # New layer
    xsetwacom set "$PAD" Button 2 "key minus"  # Zoom out
    xsetwacom set "$PAD" Button 3 "key shift plus"  # Zoom in
    xsetwacom set "$PAD" Button 4 "key control z"  # Undo
