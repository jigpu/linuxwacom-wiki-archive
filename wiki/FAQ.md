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

Both GNOME and KDE configuration GUIs are available. See [Graphical
Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

How do I calibrate my tablet or tablet PC?
------------------------------------------

See the [Calibration HOWTO](/wiki/Calibration#Calibration "wikilink").

How do I customize my tablet or tablet PC settings?
---------------------------------------------------

See the [Tablet Configuration HOWTO](/wiki/Tablet_Configuration "wikilink").

How do I get a xsetwacom configuration to survive a hotplug event?
------------------------------------------------------------------

In order to have your tablet configuration, set by run-time xsetwacom
commands, survive the use of a KVM switch or other hotplug event the
Linux Wacom Project hosts [wdaemon](wdaemon "wikilink"). This is an
update by Peter Hutterer of the original *wdaemon* by Aristeu Rozanski.
The original wdaemon is linked at the bottom of the section [Tablet
Hotplugging](/wiki/Linuxwacom_HOWTO#Hotplugging_the_device "wikilink").
Wdaemon is different from other solutions, such as daemons that monitor
DBus for hotplug events and reapply a xsetwacom script, in that it
provides a permanent virtual kernel device that looks exactly like the
physical tablet to userspace. In effect it virtually disables tablet
hotplugging.

How do I get the special features of my stylus to work?
-------------------------------------------------------

There are many different types of styli, mice, and pucks. All of them
work to varying degrees, so if you are running into trouble, please send
an email to the [mailing lists](mailing_lists "wikilink").

You may want to try the latest
[xf86-input-wacom](xf86-input-wacom "wikilink") release to see if
support for your device has been added. If you are using a build of the
[linuxwacom](linuxwacom "wikilink") backports, try the highest release
number. The latest release is likely to support more tools.

Note, tilt is only supported with protocol V tablets, which includes the
Intuos series and Cintiq 21UX/20WSX/12WX/21UX2. Any other tablets don't
have tilt, no matter if you have the tilt option in your xorg.conf or
not.

How do I rotate the screen and/or stylus on my tablet pc?
---------------------------------------------------------

See [Screen Rotation](/wiki/Tablet_PC_Setup#Screen_Rotation "wikilink").

How do I set up my tablet with Dual or Multi-Monitors?
------------------------------------------------------

See [Dual and Multi-Monitor Set
Up](/wiki/Dual_and_Multi-Monitor_Set_Up "wikilink").

I am left handed. How do I flip my tablet?
------------------------------------------

See *Rotate* in [Xsetwacom](/wiki/Xsetwacom "wikilink") and also
[Rotation](/wiki/Rotation "wikilink").

Is hotplugging supported for my USB tablet?
-------------------------------------------

Yes, but only for devices configured via [HAL or
xorg.conf.d](/wiki/Configuring_X "wikilink"). If you have your devices
configured in the [xorg.conf](/wiki/Xorg.conf "wikilink"), you can work around
this by switching virtual terminals after replugging your tablet. That
is, press Ctrl + Alt + F1 together then release them (screen turns to
console); and press Ctrl + Alt + F7 together then release them (screen
returns back to normal). On some distributions (e.g. Fedora), the
original X session runs on VT1, hence substitute the commands with Ctrl
+ Alt + F2 and Ctrl + Alt + F1.

My tablet isn't a Wacom. Will it still work?
--------------------------------------------

The xf86-input-wacom driver does support some non-wacom tablets
including those by Hanwang, N-Trig, and Waltop. For KYE and UC-Logic
graphics tablet support and additional Waltop information see the
[DIGI*mend*
project](https://sourceforge.net/apps/mediawiki/digimend/index.php?title=DIGImend).

Where is wacomcpl (the Wacom Control Panel)?
--------------------------------------------

Wacomcpl is not packaged with
[xf86-input-wacom](xf86-input-wacom "wikilink") and will not work with
it. For an explanation see [Graphical Configuration
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
