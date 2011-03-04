---
title: Xorg.conf.d
permalink: wiki/Xorg.conf.d/
layout: wiki
tags:
 - HOWTO
---

This page describes static device configuration through
[xorg.conf.d](xorg.conf.d "wikilink") snippets. Such a configuration
applies applies to hotplugged devices, as opposed to static
configuration in the
[xorg.conf](/wiki/Configuring_X#Manual_setup_in_the_xorg.conf "wikilink") or
runtime configuration with [xsetwacom](xsetwacom "wikilink"). The X
server scans several directories for *.conf* files on startup:

-   */usr/share/X11/xorg.conf.d*
    -   Files here are the shipped by the distribution and may be
        overwritten at update time. It is not recommended to place any
        user-specific configuration files here.
-   */etc/X11/xorg.conf.d*
    -   Files here are user-specific configurations. If there is no
        user-specific configuration, this directory may not exists -
        simply create it and store your configurations here.
    -   Files here are read **after** the distribution-specific patches,
        so user-specific configuration always overrides the default
        configurations.

We will create a new file in /etc/X11/xorg.conf.d called
52-wacom-options.conf to add configuration Options to it. Numbering it
52 ensures it will be run after the 50-wacom.conf in
/usr/share/X11/xorg.conf.d/.

More than one configuration snippet may apply to any device.
Configuration settings are merged, but if the same setting is specified
multiple times, only the last one is used. Usually, your distribution
ships with a number of *.conf* files that will pick the right driver for
your device. Thus, if you want to configure a particular device through
[xorg.conf.d](xorg.conf.d "wikilink") snippets, you can usually skip the
driver assignment - it's already done for you.

Matching a device
-----------------

[xorg.conf.d](xorg.conf.d "wikilink") snippets are based on several
different Match keywords. The most common ones are to match on a product
name (**MatchProduct**) or to match on a certain device path
(**MatchDevicePath**). The example below shows a snippet that applies to
any device with *either* the string "Wacom" *or* the string "Waltop" in
the product name *and* that matches a certain device path:

    Section "InputClass"
            # This is for human-readable purposes only.
        Identifier "Wacom class options"
            # Match all devices with either Wacom or Waltop in their product name
        MatchProduct "Wacom|Waltop"
            # AND match devices with a device path of /dev/input/eventX.
        MatchDevicePath "/dev/input/event*"
    EndSection

For the purpose of configuring Wacom devices, we will use the following
match keywords:

-   **MatchProduct**: matches when the device name contains the given
    substring
-   **MatchDevicePath**: matches when the device path matches the given
    path (wildcards possible)
-   **MatchDriver**: matches when the assigned device driver matches the
    given one

As mentioned above, configuration snippets can be stacked and combined.
This is particularly interesting for the **MatchDriver** option because
we can have one snippet that selects a driver for all our devices and
then a separate snippet that selects options based on what driver is
assigned. As shown in the example above, multiple **Match\*** lines
constitute a [logical
AND](http://en.wikipedia.org/wiki/Logical_conjunction) match (i.e. both
must apply), wherase using a **\|** separator in a single **Match\***
line is a [logical OR](http://en.wikipedia.org/wiki/Logical_conjunction)
match. The match strings are case sensitive.

**Examples of MatchProduct keywords** - used in the 50-wacom.conf at one
time or another

-   **USB**: "Wacom", "WACOM", "WALTOP", "Hanwang"
-   **Serial**: "Wacom serial class", "WACf", "FUJ02e5", "FUJ02e7",
    "FUJ02e9"
-   **N-Trig** (usb): "Wacom N-Trig class", "HID 1b96:0001\|N-Trig Pen"

These examples provide a fairly general match. Keep them in mind when
you write a snippet to match your device(s). More specific matches will
be shown below.

X servers 1.8 and 1.9
---------------------

With X servers 1.8 and 1.9, [xorg.conf.d](xorg.conf.d "wikilink")
configuration snippets apply only to the [ parent device
(stylus)](/wiki/How_Wacom_tablets_work#Tools "wikilink") and not dependent
devices like the eraser, cursor, or touch. In other words you can only
set tablet-wide settings. To configure dependent devices you either have
to use [
xorg.conf](/wiki/Configuring_X#Manual_setup_in_the_xorg.conf "wikilink")
sections or [xsetwacom](xsetwacom "wikilink") commands.

### Example Configuration

Only one snippet is enough as you cannot configure dependent devices.
Notice the *Driver "wacom"* line is not used (we rely on the
distribution-wide configuration). Always pick a suitable *Identifier*.
Identifiers of matching input classes are printed into the Xorg.log and
allows you to determine at a glance whether your match lines worked and
your Options are being applied. Identify each input tool's device name
with [xsetwacom list](xsetwacom "wikilink"). This will let you determine
what keyword(s) will be useful for a match.

    # Because Bamboos provide two separate kernel devices, we can 
    # actually have two snippets for two different devices here.
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

X server 1.10
-------------

With *X server 1.10* you can configure dependent devices. In other words
you are able to set each input tool up in its own snippet like a section
in the xorg.conf. The advantage is that these settings will be reapplied
to each device when the device is plugged in at run-time.

### Example Dependent Device Static Configuration

Identify each input tool's device name with [xsetwacom
list](xsetwacom "wikilink"). We use the **MatchDriver** tag here to
simplify device selection. This way, we rely on the distribution-wide
match tags to apply the right driver to our device and then only match
on those devices that have the wacom driver applied to them.

    Section "InputClass"
        Identifier "Wacom Bamboo stylus options"
            MatchDriver "wacom"
            MatchProduct "stylus"

            # Apply custom Options to this device below.
        Option "RawSample" "20"
        Option "PressCurve" "0 10 90 100"
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
        Option "PressCurve" "5 0 100 95"
        Option "TopX" "-10"
        Option "TopY" "-20"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom Bamboo touch options"
            MatchDriver "wacom"
            MatchProduct "touch"

            # Apply custom Options to this device below.
        Option "ScrollDistance" "18"
        Option "TapTime" "220"
    EndSection

    Section "InputClass"
        Identifier "Wacom Bamboo pad options"
            MatchDriver "wacom"
            MatchProduct "pad"

            # Apply custom Options to this device below.
        Option "Button1" "key ctrl t"
        Option "Button2" "key backspace"
        Option "Button3" "3"
        Option "Button4" "1"
    EndSection

You would only use the snippets corresponding to the input tools you
have, of course. Clearly if you are plugging in two or more tablets they
will have the same Option settings and more specific matches may be
required.
