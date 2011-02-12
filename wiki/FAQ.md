---
title: FAQ
permalink: wiki/FAQ/
layout: wiki
---

Why linuxwacom and xf86-input-wacom?
------------------------------------

[xf86-input-wacom](xf86-input-wacom "wikilink") was created as a fork of
[linuxwacom](linuxwacom "wikilink") for the following reasons:

-   linuxwacom has collected some amount of cruft over the years,
    including some amount of ifdefs that attempt to support every X
    server version since XFree86-4.
-   xf86-input-wacom is an X11-only driver, the kernel driver is not
    part of xf86-input-wacom.
-   Ping has expressed a desire to keep linuxwacom in CVS, in part to
    avoid disruption for current users of the linuxwacom releases.
-   The use of git as SCM should encourage more outside participation,
    so far Ping has done most of the heavy lifting in the driver. A more
    community-driven development model is desirable. We have already
    seen numerous patches from others and are very happy about this.

xf86-input-wacom is the preferred driver for X Servers 1.7 and later.
linuxwacom will <b>not</b> compile against these servers without
additional patches.

Which devices are supported?
----------------------------

How do I configure my device?
-----------------------------

See [Building the driver](/wiki/Building_the_driver "wikilink") and
[Configuring X](/wiki/Configuring_X "wikilink")

Are there any graphical configuration tools?
--------------------------------------------

See [Graphical Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

How do I calibrate my tablet or tablet pc?
------------------------------------------

See the [Calibration HOWTO](/wiki/Calibration#Calibration "wikilink").

How do I get the special features of my stylus to work?
-------------------------------------------------------

There are many different types of styli, mice, and pucks. All of them
work to varying degrees, so if you are running into trouble, please send
an email to the [mailing lists](mailing_lists "wikilink").

If you are using the stable build of the
[linuxwacom](linuxwacom "wikilink") backports, try the beta. Many of the
tools have been recently added and have not yet been moved to the
production release.

Note, tilt is only supported with protocol V tablets, which include
Intuos series and Cintiq 21UX/20WSX/12WX to date (July. 31, 2009). Any
other tablets don't have tilt no matter you have the tilt option in your
xorg.conf or not.

How do I rotate the screen and/or stylus on my tablet pc?
---------------------------------------------------------

See [Screen Rotation](/wiki/Tablet_PC_Setup#Screen_Rotation "wikilink").

How do I set up my Bamboo Pen & Touch?
--------------------------------------

See [Bamboo Pen &
Touch](/wiki/Wacom_Tablet_Set_Up#Bamboo_Pen_&_Touch "wikilink").

How do I set up my tablet with Dual or Multi-Monitors?
------------------------------------------------------

See [Dual and Multi-Monitor Set
Up](/wiki/Dual_and_Multi-Monitor_Set_Up "wikilink").

I am left handed. How do I flip my tablet?
------------------------------------------

See *Rotate* in [Xsetwacom](/wiki/Xsetwacom "wikilink") and also
[Rotation](/wiki/Rotation "wikilink").

Is hot plugging supported for my USB tablet?
--------------------------------------------

Yes, but only for devices configured via [HAL or
xorg.conf.d](/wiki/Configuring_X "wikilink"). If you have your devices
configured in the [xorg.conf](/wiki/Xorg.conf "wikilink"), you can work around
this by switching virtual terminals after replugging your tablet. That
is, press Ctrl + Alt + F1 together then release them (screen turns to
console); and press Ctrl + Alt + F7 together then release them (screen
returns back to normal). On some distributions (e.g. Fedora), the
original X session runs on VT1, hence substitute the commands with Ctrl
+ Alt + F2 and Ctrl + Alt + F1.

Where is wacomcpl (the Wacom Control Panel)?
--------------------------------------------

Wacomcpl is not packaged with
[xf86-input-wacom](xf86-input-wacom "wikilink") and will not work with
it. Work is proceeding on a replacement integrated into the GNOME
Desktop environment. For links and an explanation see [Graphical
Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

Who is responsible for this code?
---------------------------------

You are. Linux Wacom is an open source project and driven by Linux users
with Wacom tablets such as yourself. John Joganic took up the code in
November of 2002 and created this project. The source code was written
by at least two dozen individuals, most of them are not directly
involved in the project any more. Ping Cheng has assumed the project
administrator since 2003. You can help out by testing, reporting bugs,
documentation on this wiki and of course by writing patches (see the
[mailing lists](mailing_lists "wikilink")).
