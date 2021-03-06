---
title: Rotation
permalink: wiki/Rotation/
layout: wiki
tags:
 - HOWTO
---

The wacom driver supports rotation by 90 degree angles. The rotation can
be either applied statically (through the [configuration
files](/wiki/Configuring_X "wikilink")) or at runtime (e.g. through
[xsetwacom](xsetwacom "wikilink")). The four valid settings are:

-   **none**: the tablet is not rotated in software and uses its natural
    rotation.
-   **half**: the tablet is rotated by 180 degrees (upside-down)
-   **cw**: the tablet is rotated 90 degrees clockwise
-   **ccw**: the tablet is rotated 90 degrees counter-clockwise

Note that rotation is a tablet-wide option. If you rotate one input tool
all other tools associated with the same tablet are rotated to the same
orientation.

Examples
--------

### Left Handed Tablet Orientation

The Bamboo Pen & Touch will be used as an example since it has three
tools: stylus, eraser, and touch. Find the "Device names" using in a
terminal:

    xinput list

The "Device name" for stylus might be "Wacom BambooFun 2FG 4x5 Pen
stylus". It will be without the quotes but you need the quotes for the
[xsetwacom](xsetwacom "wikilink") command. And so on for eraser and
touch. Once the "Device names" are determined use them in
[xsetwacom](xsetwacom "wikilink") commands like so:

    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" rotate half 

    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen eraser" rotate half

    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" rotate half

While the [xsetwacom](xsetwacom "wikilink") settings will apply once
entered into a terminal they will not last through a restart. For that
you could create an executable script with the commands and add it to
your Startup Applications.

### Tablet PC Rotation Script

Again determine your "Device names" with 'xinput list' as in the example
above. Then substitute them for stylus, eraser, and touch (if you have
it) in the following shell script. Only use the
[xsetwacom](xsetwacom "wikilink") commands for the devices you have.

    #!/bin/sh

    # Find the line in "xrandr -q --verbose" output that contains current screen orientation and "strip" out current orientation.

    rotation="$(xrandr -q --verbose | grep 'connected' | egrep -o  '\) (normal|left|inverted|right) \(' | egrep -o '(normal|left|inverted|right)')"

    # Using current screen orientation proceed to rotate screen and input devices.

    case "$rotation" in
        normal)
        # rotate to the left
        xrandr -o left
        xsetwacom set stylus rotate ccw
        xsetwacom set eraser rotate ccw
        xsetwacom set touch rotate ccw
        ;;
        left)
        # rotate to inverted
        xrandr -o inverted
        xsetwacom set stylus rotate half
        xsetwacom set eraser rotate half
        xsetwacom set touch rotate half
        ;;
        inverted)
        # rotate to the right
        xrandr -o right
        xsetwacom set stylus rotate cw
        xsetwacom set eraser rotate cw
        xsetwacom set touch rotate cw
        ;;
        right)
        # rotate to normal
        xrandr -o normal
        xsetwacom set stylus rotate none
        xsetwacom set eraser rotate none
        xsetwacom set touch rotate none
        ;;
    esac

This will cause the screen to rotate through 360 degrees
counter-clockwise in four 90 degree steps. Again place the script in an
executable file. You could then add it to a launcher or bind a key to
the script. If you drag the launcher into a panel it will execute with a
single click. Of course you can easily simplify the script to only
rotate to one preferred direction when in tablet mode and back to none
when in laptop mode.
