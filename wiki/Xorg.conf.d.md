---
title: Xorg.conf.d
permalink: wiki/Xorg.conf.d/
layout: wiki
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
multiple times, only the last one is used.

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
assigned.

**Sample of MatchProduct keywords** - used in the 50-wacom.conf at one
time or another

-   **USB**: "Wacom", "WACOM", "WALTOP", "Hanwang"
-   **Serial**: "Wacom serial class", "WACf", "FUJ02e5", "FUJ02e7"
-   **N-Trig** (usb): "Wacom N-Trig class", "HID 1b96:0001\|N-Trig Pen"

The match strings are case sensitive. As demonstrated by the N-Trig
keywords you can string keywords together using **\|** to separate them,
another e.g. "Wacom\|WACOM\|Hanwang", as long as they are enclosed by
quotes. As you would expect the 50-wacom.conf keywords, with the
exception of the second strung together N-Trig one, provide a fairly
general match. Keep them in mind when you write a snippet to match your
device(s). More specific matches will be shown below.

-   **X server version**: if you don't know your version number enter
    *Xorg -version* in a terminal.

X servers 1.8 and 1.9
---------------------

With X servers 1.8 and 1.9, [xorg.conf.d](xorg.conf.d "wikilink")
configuration snippets apply only to the [ parent device
(stylus)](/wiki/How_Wacom_tablets_work#Tools "wikilink") and not dependent
devices like the eraser, cursor, or touch. In other words you can only
set tablet wide settings. To configure dependent devices you either have
to use [
xorg.conf](/wiki/Configuring_X#Manual_setup_in_the_xorg.conf "wikilink")
sections or [xsetwacom](xsetwacom "wikilink") commands.

### Sample Static Configuration

Because you can not configure dependent devices you will just need one
snippet. Notice the *Driver "wacom"* line is not needed or included and
that the *Identifier* has *options* added to it. That allows you to
determine at a glance that your match lines worked and your Options are
being applied in Xorg.0.log (in /var/log). Identify each input tool's
"device name" in [xinput list](xinput "wikilink"). This will let you
determine what keyword(s) will be useful for a match. You could use one
or more keywords from the *Sample of MatchProduct keywords* list above.

Bamboo Pen & Touch tablets and USB tablet PCs appear to be the exception
because they can have two snippets, but that is an illusion. The
explanation is they are seen by the system as 2 devices, stylus/eraser &
touch/pad for the Bamboo P&T and stylus/eraser & touch for USB tablet
PCs. Because it is the most "complicated" case we will use the Bamboo
P&T in the example. Again remember for tablets without touch and Serial
tablet PCs only one snippet is needed.

After identifying each input tool's "device name" in *xinput list* we
see there are two devices to match. So rather than using a standard USB
keyword for the match, we decide to use the *root* name for each parent
and dependent device. In the Bamboo P & T case the *root* "device names"
include *Pen* or *Finger*. Into your new 52-wacom-options.conf file
located in /etc/X11/xorg.conf.d to add a *PressureCurve* and *Area*
Options enter:

    Section "InputClass"
            # This is for human-readable purposes only.
        Identifier "Wacom class options"
            # Match all Wacom input tools that have "BambooFun 2FG 4x5 Pen" in the "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Pen"
            # AND match devices with a device path of /dev/input/eventX.
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "PressCurve" "0 10 90 100"
        Option "TopX" "0"
        Option "TopY" "0"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom class options"
            # Match all Wacom input tools that have "BambooFun 2FG 4x5 Finger" in the "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Finger"
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "TopX" "0"
        Option "TopY" "0"
        Option "BottomX" "480"
        Option "BottomY" "320"
    EndSection

**Note** the static configuration files still use the "old" parameter
names. This demonstrates you can configure Options that apply to both
the parent & dependent devices, such as *PressureCurve* to stylus/eraser
and the *Area* coordinates to stylus/eraser & touch. The *Area*
coordinates don't affect the pad of course.

X server 1.10
-------------

With *X server 1.10* you can configure dependent devices. In other words
you are able to set each input tool up in its own snippet like a section
in the xorg.conf. The advantage is that these settings will last through
a hot plug. How to do that follows.

### Sample Dependent Device Static Configuration

Again identify each input tool's "device name" in [xinput
list](xinput "wikilink"). You'll use each in a match line for it's
snippet. That guarantees specificity and eliminates the need for an
extra line to do a generic match to Wacom or whatever. Plus you can use
more than one tablet as long as their "device names" are different,
which you can't do using *eraser* or *cursor*, etc. In each snippet
we'll enter the Options equivalent to the xsetwacom parameters used in
the [Sample Runtime
Script](/wiki/Tablet_Configuration#Sample_Runtime_Script "wikilink")
configuration just as an example. Notice again the wacom driver line is
not used and we modify the Identifier.

Into your new 52-wacom-options.conf file located in /etc/X11/xorg.conf.d
enter:

    Section "InputClass"
            # This is for human-readable purposes only.
        Identifier "Wacom class options"
            # Match the Wacom input tool that has this specific "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Pen stylus"
            # AND match the device with a device path of /dev/input/eventX.
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "RawSample" "20"
        Option "PressCurve" "0 10 90 100"
        Option "TopX" "-10"
        Option "TopY" "-20"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom class options"
            # Match the Wacom input tool that has this specific "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Pen eraser"
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "RawSample" "20"
        Option "PressCurve" "5 0 100 95"
        Option "TopX" "-10"
        Option "TopY" "-20"
        Option "BottomX" "14720"
        Option "BottomY" "9200"
    EndSection

    Section "InputClass"
        Identifier "Wacom class options"
            # Match the Wacom input tool that has this specific "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Finger touch"
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "ScrollDistance" "18"
        Option "TapTime" "220"
    EndSection

    Section "InputClass"
        Identifier "Wacom class options"
            # Match the Wacom input tool that has this specific "device name".
        MatchProduct "Wacom BambooFun 2FG 4x5 Finger pad"
        MatchDevicePath "/dev/input/event*"

            # Apply custom Options to this device below.
        Option "Button1" "key ctrl t"
        Option "Button2" "key backspace"
        Option "Button3" "3"
        Option "Button4" "1"
    EndSection

### 52-wacom-options.conf Template for xorg.conf.d

Now let's use a different approach to matching. Starting with X server
1.10 *MatchDriver* became available. This is very useful because the
50-wacom.conf has already matched the Wacom device(s) and device input
events to the *wacom* driver. All we need to do in the
52-wacom-options.conf is match to the wacom driver and pick up the input
tools already on it. This allows us to have a general xorg.conf.d
template, like we do with the [xorg.conf](xorg.conf "wikilink").

    Section "InputClass"
            # This is for human-readable purposes only.
        Identifier "Wacom stylus options"
            # Match to this Wacom input tool on the wacom driver.
        MatchDriver "wacom|stylus"

            # Apply custom Options to this device below.

    EndSection

    Section "InputClass"
        Identifier "Wacom eraser options"
            # Match to this Wacom input tool on the wacom driver.
        MatchDriver "wacom|eraser"

            # Apply custom Options to this device below.

    EndSection

    Section "InputClass"
        Identifier "Wacom cursor options"
            # Match to this Wacom input tool on the wacom driver.
        MatchDriver "wacom|cursor"

            # Apply custom Options to this device below.

    EndSection

    Section "InputClass"
        Identifier "Wacom touch options"
            # Match to this Wacom input tool on the wacom driver.
        MatchDriver "wacom|touch"

            # Apply custom Options to this device below.

    EndSection

    Section "InputClass"
        Identifier "Wacom pad options"
            # Match to this Wacom input tool on the wacom driver.
        MatchDriver "wacom|pad"

            # Apply custom Options to this device below.

    EndSection

You would only use the snippets corresponding to the input tools you
have, of course. Clearly if you are plugging in two or more tablets they
will have the same Option settings. That can be problematic for the pad,
since unless they are the same model the tablet buttons (pads) are
likely different. In which case you will need to develop specific
matches for each model's pad.
