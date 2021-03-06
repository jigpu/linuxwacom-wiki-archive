---
title: XInput Driver for Linuxwacom
permalink: wiki/XInput_Driver_for_Linuxwacom/
layout: wiki
tags:
 - Linuxwacom
---

The xf86Wacom driver is divided into seven distinct pieces.

:\* XInput application interface

:\* module management and configuration

:\* data path and event notification

:\* raw data filter

:\* supporting user space configuration

:\* compatibility operations for 3.x and 4.x versions of XFree86

:\* device-specific functionality

**xf86Wacom.c**

The core elements of the driver, particularly the functions necessary to
implement the XInput interface are contained in xf86Wacom.c. Here, the
devices declared in the InputDevice sections of XF86Config are opened,
closed, and configured. This is also where the device capabilities are
declared and valuator ranges are set.

**wcmConfig.c**

All options supported by the driver are queried and set in wcmConfig.c.
This includes values like Threshold, BaudRate, and Suppress. Some
parameters are global to all tablets, while others are specific to a
single tablet. Most parameters apply to a particular class of tool for a
given tablet. It is also possible to subdivide the tablet into discrete
regions, and devices may be assigned serial numbers to uniquely identify
a particular tool.

Code for loading and unloading the module is also contained in
wcmConfig.c. The driver can be loaded into memory as a module or
compiled directly into the XFree86 libraries. Additionally, this driver
can be compiled for the 3.x and 4.x version of XFree86 as well as X.org
6.x and 7.x. Some care has been taken to preserve the ability to compile
for 3.x; however, actual testing is not generally performed. For all
intents and purposes, only the 4.x version of XFree86 and X.org are
actively supported.

**wcmCommon.c**

The functionality which implements the data path, filters, and event
notifications is located in wcmCommon.c. In principal, all devices
should report their data to the xf86WcmEvent function in precisely the
same way: unfiltered, but within valid ranges, and normalized. As an
example, some devices report pressure internally as -128 to 127;
normalized pressure values would be from 0 to 255 for such a device.
Once in the data path, filtering for suppression, rotations,
ZAxisMapping, and the like are performed as needed. Ultimately, the
event is dispatched to the XInput subsystem and on to the appropriate
client applications.

**wcmFilter.c**

Pressure sensitivity is one of the essential feature of Wacom tablet.
This code enables user to customize pressure output to their own
preference, such as firm, mild, soft. This code also enables user to
filter tablet raw data. Changing the RawSample parameter is not
recommanded since it normally reduces the data rate which reduces data
accuracy. RawSample may be used to deal with jitter of unknown cause. If
this is your case, you most probably need to test a range of RawSamples
to figure out the most suitable one.

**wcmXCommand.c**

To facilitate the configuration and access to the driver from user
space, a command line utility program called xsetwacom is introduced.
wcmXCommand.c is the driver side interface to receive and reply to
xsetwacom calls. Every command in xsetwacom is processed here before it
goes into the driver to set or retrieve information. Configuration data
is validated here.

**wcmCompat.c**

In order to maintain compatibility with the XFree86 3.x build, the names
of many routines are aliased using macros or implemented depending on
the platform and version. As an example, xf86WcmWait is implemented
differently for both major versions of XFree86. In contrast,
xf86WcmOpenTablet is simply aliased to xf86OpenSerial for version 4.x
while fully implemented using the low-level open call in version 3.x.

**wcmSerial.c, wcmUSB.c, and wcmISDV4.c**

At present, there are three different classes of tablets: auto-detecting
serial tablets, Linux Input System based USB tablets, and ISDV4 protocol
based serial Tablet PC's. Each tablet class implements a detection,
initialization, and device reading function. Using this model, new
device classes can be added easily.

<center>
<small> Copyright (C) 2002-2011 - LinuxWacom

This website and its contents are licensed under the GNU GENERAL PUBLIC
LICENSE. </small>

</center>
