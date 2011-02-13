---
title: Overview for Third Party Developers
permalink: wiki/Overview_for_Third_Party_Developers/
layout: wiki
tags:
 - Linuxwacom
---

There are two main points in an application where modifications may be
made to support tablets:

**1) Initialization**

During initialization you need to determine if a tablet is attached.

If there is a tablet, you need to get static information about the
tablet, such as tablet dimensions, coordinate resolution, pressure
range, tangential pressure range, and other features.

**2) Event Handler**

Here, you will get data from the tablet and find information about the
tablet device currently in use. The tablet data is used for line
rendering or navigation. The device information can be used to change
tools or screen cursors. Tablets produce three event types: pointer
events, button events, and valuator events.

**2a) Button and Pointer events**

Tablet button and pointer events are indistinguishable from mouse button
and pointer events. Since most applications use these events for drawing
and navigation the tablet works transparently.

**2b) Valuator events**

The real tablet data is contained in valuator events. High resolution X
and Y data, pressure, proximity, absolute or relative mode, and other
information is available here. This information can be used for advanced
navigation, such as using 4D Mouse rotation to rotate an object. It can
also be used for advanced line rendering, such as modulation line width
with pressure. The descriptions below explain special treatment of
valuator events.

**Controlling the Driver**

To change the driver's default values, there are options which can be
defined through the configuration file, XF86Config, XF86Config-4, or
xorg.conf under directory /etc/X11. Please refer to [Adding the
InputDevices](/wiki/Linuxwacom_HOWTO#Adding_the_InputDevices "wikilink") for
examples and a list of available options.

**Getting Data From the Driver**

The Linux Wacom driver for serial tablet is implemented as an XFree86
Input Extension device. The USB tablet is first loaded through wacom.o
in the kernel and then communicated with the XFree86 module,
xf86wacom.o, to fulfill the tablet events.

Device Stylus handles all kinds of the pens, such as the Grip Pen,
AirBrush, InkingPen, etc.

Device Cursor handles all of the pucks, such as the 4D Mouse, 2D Mouse,
Lens Cursor, etc.

Device Eraser handles the eraser button of a pen or airbrush device.

The driver sends events through the XFree86 event queue to applications.
The Wacom tablet sends several types of events to the XFree86 event
queue. The most immediately obvious are pointer and button events, which
can be captured by applications to determine the current location and
active buttons. Sample code shows how these values are defined.

The valuators are reported as follow:

:\* X position

:\* Y position

:\* Pressure

:\* Tilt X; or rotation for 4D mouse; or Tablet Left Menu strip (Intuos3
and Cintiq 21UX)

:\* Tilt Y; or wheel for 4D mouse; or Tablet Right Menu strip (Intuos3
and Cintiq 21UX)

:\* Airbrush Wheel position; or Marker Pen rotation (Intuos3 and Cintiq
21UX); or Touch Ring (Bamboo)

**Coordinate Processing**

**Features That Have Been Around for a Long Time**

Pressure, Eraser, and Tilt are features that have been around for some
time. A large installed base of tablets support these capabilities and,
if you have not already done so, you can increase the power and
performance of your application by implementing these features.

**Pressure**

There are many obvious uses for pressure. It can be used to modulate the
width of a stroke, opacity, color, etc. Be sure to scale the pressure
value to use the full range of pressure. Don't assume a maximum pressure
level, check the tablet's capabilities to find the maximum pressure
value (Check the maximum for the third valuator). Donâ€™t think of
pressure as only useful for drawing. For example: Use pressure to
control scrolling speed in a scroll bar. Or maybe Click-drag with low
pressure does a move; click-drag with high pressure does a copy-move.
Pressure is reported in the third valuator.

**Eraser**

The obvious purpose of the eraser is to use it as an erasing tool. But
you can change the function of the eraser to perform more advanced
tasks. Using the stylus tip in conjunction with the eraser, you can
easily perform two opposing functions such as dodge and burn, or ink and
bleach. The eraser can also function independently as any tool selected
in the application.

**Tilt**

Stylus tilt has several uses, but the most obvious is to change brush
shape. A good example of using stylus tilt to change brush shape is the
Wacom PenTools Virtual Airbrush tool for the Macintosh or PC. Tilt can
also be used for object rotation, or as a joystick. X and Y tilt are in
the forth and fifth valuators respectively.

**Airbrush Fingerwheel**

The Intuos and Intuos2 series tablets have a device that is similar to
an airbrush. It not only supports pressure on the tip, but also has a
fingerwheel on the side to simultaneously vary a second value. For
example, using the fingerwheel, you can vary line width with pressure
and ink density.

The value of the fingerwheel on the side of the Airbrush is reported in
the sixth valuator. The full range is from 0 when pushed forward to 1023
when pulled back, but be sure to use the value contained in the
max\_value field of the valuator to get the full range to allow for
future changes.

**4D Mouse Rotation**

The Intuos and Intuos2 series tablets have a device that is similar to a
puck. In addition to normal puck parameters, this puck supports axial
rotation and a wheel. For example, using the 4D Mouseâ€™s rotation you
can rotate the paper, or an object, and zoom with the wheel.

4D Mouse axial rotation is reported in the forth valuator. Values in
this field range from -900 to 900 in 0.2 degree increments.

**4D Mouse Wheel**

Uses for the 4D Mouse wheel include zooming, scrolling, and 3D
navigation. The 4D Mouse wheel value is reported in the sixth valuator.
The wheel varies from 1023 when pushed forward to -1023 when pulled back
and snaps to the middle, which is of course zero, when released. But as
usual, make sure to use the range reported in min\_value and max\_value
fields of the valuator to allow for future changes.

**2D Mouse Wheel**

Intuos2 series tablets have 2D Mouse that is similar to a puck. In
addition to normal puck parameters, this puck supports a wheel. This
wheel reports relative movement value in the sixth valuator. The wheel
value is 1 when pushed forward and -1 when pulled back. This wheel event
is translated into 4th and 5th button events.

**Device ID for Intuos Series and Cintiq 21UX**

         
            0x812: /* Intuos2 ink pen */
            0x801: /* Intuos3 Inking pen */
            0x012: /* Inking pen */
            0x20802: /* Intuos4 Inking Pen */

            0x822: /* Intuos Pen */
            0x852: /* Intuos2 Grip Pen */
            0x842: /* Designer Pen */
            0x823: /* Intuos3 Grip Pen */
            0x813: /* Intuos3 Classic Pen */
            0x885: /* Intuos3 Marker Pen */
            0x022: /* Intuos Pen */
            0x802: /* Intuos4 Grip Pen Eraser */
            0x804: /* Intuos4 Marker Pen */
            0x40802: /* Intuos4 Classic Pen */

            0x832: /* Intuos2 stroke pen */
            0x032: /* Stroke pen */

            0x007: /* 2D Mouse */
            0x094: /* 4D Mouse */
            0x017: /* Intuos3 2D Mouse */
            0x806: /* Intuos4 Mouse */

            0x096: /* Lens cursor */
            0x097: /* Intuos3 Lens cursor */
            0x006: /* Intuos4 Lens cursor */

            0x82A: /* Intuos Eraser */
            0x85A: /* Intuos2 Eraser */
            0x91A: /* Intuos Eraser */
            0xD1A: /* Intuos Eraser */
            0x0FA: /* Intuos Eraser */
            0x82B: /* Intuos3 Grip Pen Eraser */
            0x81B: /* Intuos3 Classic Pen Eraser */
            0x91B: /* Intuos3 Airbrush Eraser */
            0x80c: /* Intuos4 Marker Pen Eraser */
            0x80a: /* Intuos4 Grip Pen Eraser */
            0x4080a: /* Intuos4 Classic Pen Eraser */
            0x90a: /* Intuos4 Airbrush Eraser */

            0x112: /* Airbrush */
            0x912: /* Intuos2 Airbrush */
            0xD12: /* Intuos Airbrush */
            0x913: /* Intuos3 Airbrush */
            0x902: /* Intuos4 Airbrush */

Device ID for all the other tablets

         
            0x002: /* pen */
            0x006: /* cursor */
            0x00A: /* eraser */
            0x003: /* touch */
            0x00F: /* pad */

<center>
<small> Copyright (C) 2002-2011 - LinuxWacom

This website and its contents are licensed under the GNU GENERAL PUBLIC
LICENSE. </small>

</center>
