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
[xsetwacom](xsetwacom "wikilink")).

The four valid settings are:

-   **none**: the tablet is not rotated in software and uses its natural
    rotation.
-   **half**: the tablet is rotated by 180 degrees (upside-down)
-   **cw**: the tablet is rotated 90 degrees clockwise
-   **ccw**: the tablet is rotated 90 degrees counter-clockwise

Note that rotation is tool-specific. If you rotate the stylus tool, the
eraser tool and all others still have the original rotation. To rotate
the complete tablet, rotate all tools.

Examples
--------

### Left Handed Tablet Orientation

The Bamboo Pen & Touch will be used as an example since it has three
tools: stylus, eraser, and touch. Find the "Device names" using in a
terminal:

    xinput list

The "Device name" for stylus might be "Wacom BambooFun 2FG 4x5 Pen
stylus". It will be without the quotes but you need the quotes for the
xsetwacom command. And so on for eraser and touch. Once the "Device
names" are determined use them in xsetwacom commands like so:

    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" rotate half 

    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen eraser" rotate half

    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" rotate half

While the xsetwacom settings will apply once entered into a terminal
they will not last through a restart. For that you could create an
executable script with the commands and add it to your Startup
Applications.
