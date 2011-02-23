---
title: How Wacom tablets work
permalink: wiki/How_Wacom_tablets_work/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

Wacom tablets are available in serial and USB configurations. They are
also sold as embedded products in certain tablet PC's. I will cover all
three types, but the serial case is the most straightforward, so we'll
start there.

Tablet types
============

Serial Tablets
--------------

When a Wacom serial tablet is connected to a serial port, software can
interact with it directly by opening the appropriate device, usually
/dev/ttyS0. The [Wacom X driver](xf86-input-wacom "wikilink") does
precisely this, and all stylus movements are converted into X input
events for programs like the Gimp to turn into fluid brush strokes.

Wacom tablets are capable of handling a number of different data
protocols, and the Linux Wacom Project code currently utilitizes two,
Wacom Protocol IV and Protocol V. Each operates with a fixed-sized
packet, the length of which depends on the model and ROM version.

When the serial tablet is reset, it defaults to a standard baud rate,
often 38400 baud. From there, the device type can be queried, and if the
tablet supports it, the baud rate increased to a higher value.
Additionally, model parameters like tablet size can be queried directly
to determine which features are available.

Once configured, the tablet streams data back to the application as
tools are brought into and out of proximity, are pressed against the
tablet surface, or are tilted or inverted.

If you have trouble with your serial tablet you can try some of the
suggestions found in the [Serial Help](/wiki/Serial_Help "wikilink") page.

USB Tablets
-----------

Initially at least, the USB Wacom tablet is an HID compliant device, and
when first connected to the computer, will identify itself as such.
Unfortunately, this is not what you want because in this mode, you will
not get any of the fancy features. The hid-core.c, mousedev.c, and
usbmouse.c kernel drivers contain exceptions for wacom devices; when the
device is detected, they ignore the tablet. In this way, the more
sophisticated wacom driver has the opportunity to assume control.

The first thing that the driver does is register itself with the USB
subsystem and wait for work to do. When the user plugs the device in, or
the device is first detected, the USB subsystem shops the vendor and
device identifier around, checking it against different drivers. The
wacom driver takes responsibility for the tablet and then notifies the
event system that it will be providing data. It then asks the tablet to
switch from HID-compliant mode to "mode 2", a wacom-specific protocol
which allows for values like pressure, Z rotation, and tilt. As
information arrives, the wacom driver dutifully converts the data into
real-world values and hands it on to the event system.

From here, any usermode application can get access to the event data by
opening /dev/input/event0. A stream of events including mouse movements,
clicks, and proximity updates can be read from the device. Similar to
the serial case, the [Wacom X driver](xf86-input-wacom "wikilink") has
the ability to read this device, and performs filtering on the data
before converting the Linux input events into X input events.

By breaking the responsibility for the data into three distinct levels,
the kernel code remains simple and robust, the applications generalized,
and the fancy features commonly accessible to all GUI applications in
the X window system itself. This document walks down the entire data
path from the USB kernel driver to the gimp application.

Embedded Devices - Tablet PC with Wacom Digitizer
-------------------------------------------------

Refer to the [Tablet PC](/wiki/Tablet_PC "wikilink") page for detail.

Tools
=====

All device types expose only a single kernel device to the
[xf86-input-wacom X driver](xf86-input-wacom_X_driver "wikilink"). That
driver splits the data coming from this kernel device into multiple X
devices or *tools*.

X applications such as the [GIMP](http://www.gimp.org)\] can then detect
which tool an event is coming from, assign different settings to
different tools, etc.

Stylus
------

This is the default tool all wacom tablets have. It is the tip of the
physical pen plus (if available the buttons on the stylus itself).

Styli usually support x, y, and pressure, with some models also
supporting tilt. Styli have one, two or three buttons.

Eraser
------

The back on some physical pens is a separate button. This button is
provided as a separate X device.

Erasers usually support x, y, and pressure, with some models also
supporting tilt. Erasers have one button.

Not all Wacom models provide a pen with an eraser.

Cursor
------

The cursor tool is the mouse-like device compatible with some tablets.

The cursor tool usually supports x, y and rotation.

Not all Wacom models support a cursor tool.

Pad
---

The pad tool is the physical tablet itself excluding the drawing area,
including the buttons and the scroll ring or scroll strips (if
applicable).

The pad tool usually provides a number of buttons, depending on the
model. The pad also provides the coordinates of the scroll ring or
scroll strips as raw data.

Touch
-----

The touch tool is the input caused by finger touches.

The touch tool usually support x and y.

Not all Wacom models support a touch tool.
