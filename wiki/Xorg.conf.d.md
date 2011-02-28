---
title: Xorg.conf.d
permalink: wiki/Xorg.conf.d/
layout: wiki
---

### xorg.conf.d

Static configuration in *xorg.conf.d* permits USB hotplugging, as
opposed to static configuration in the xorg.conf or runtime
configuration with [xsetwacom](xsetwacom "wikilink"). Most, if not all,
distributions recommend placing custom .conf files in /usr/share/X11/
rather than in /usr/share/X11/xorg.conf.d/. This is because .conf files,
such as the 50-wacom.conf, in /usr/share/X11/xorg.conf.d/ can be over
written by distribution updates. Since adding Options to the
50-wacom.conf file in /usr/share/X11/xorg.conf.d/ could be consider
customizing it, we will create a new file in /usr/share/X11/ called
52-wacom-options.conf to add configuration Options to. Numbering it 52
ensures it will be run after the 50-wacom.conf in
/usr/share/X11/xorg.conf.d/.

#### X server 1.8 & 1.9

With X servers 1.8 & 1.9 you can only use Options that apply to the
Master device (stylus) and not dependent devices like the eraser,
cursor, or touch (for serial tablet PCs). In other words you can only
set tablet wide settings. To configure dependent devices you either have
to use xorg.conf sections or xsetwacom commands.

First you will need to determine which snippet your tablet or tablet PC
is using. USB tablets or tablet PCs will use the USB snippet, which
we'll use in the following examples. The N-Trig snippet is self evident.
If your not sure what Serial snippet applies simply comment out the one
you think is probably not needed by your serial tablet PC and reboot. If
your digitizer is still working you were right. The snippet being used
by your device is the snippet you need to add your Options to, or to use
as the template for the snippet or snippets you will add to your
52-wacom-options.conf.

##### Sample Static Configuration

Because you can not configure dependent devices you will just need one
snippet, based on the one your device is using. Notice the *Driver
"wacom"* line is not needed or included and that the *Identifier* has
*options* added to it. That allows you to determine at a glance that
your match lines worked and your Options are being applied in Xorg.0.log
(in /var/log). Use the standard match line for your snippet, which would
be *MatchProduct "Wacom\|WACOM\|WALTOP\|Hanwang"* in the USB snippet.

Bamboo Pen & Touch tablets and USB tablet PCs appear to be the
exception, but that is an illusion. That's because they are seen by the
system as 2 devices, stylus/eraser & touch/pad for the Bamboo P&T and
stylus/eraser & touch for USB tablet PCs. Because it is the most
"complicated" case we will use the Bamboo P&T in the example. Identify
each input tool's "device name" in [xinput list](xinput "wikilink").
Since there are two devices to match, rather than using the standard USB
match line, we'll use the *root* name for each Master and dependent
device. In the Bamboo P & T case the *root* "device names" include *Pen*
or *Finger*.

Into your new 52-wacom-options.conf file located in /etc/X11/xorg.conf.d
to add a *PressureCurve* and *Area* Options enter:

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
the Master & dependent devices, such as *PressureCurve* to stylus/eraser
and the *Area* coordinates to stylus/eraser & touch. The *Area*
coordinates don't affect the pad of course.

#### X server 1.10

With *X server 1.10* you can configure dependent devices. In other words
you are able to set each input tool up in its own snippet like a section
in the xorg.conf. The advantage is that these settings will last through
a hot plug. How to do that follows.

##### Sample Dependent Device Static Configuration

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