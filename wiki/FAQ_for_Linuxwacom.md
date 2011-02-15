---
title: FAQ for Linuxwacom
permalink: wiki/FAQ_for_Linuxwacom/
layout: wiki
tags:
 - Linuxwacom
---

**This FAQ is intended only for questions relating to the linuxwacom
drivers on legacy systems** not dealt with by the LWP's
**[FAQ](/wiki/FAQ "wikilink")**. It is not intended to replace or duplicate
**the [LWP's FAQ](/wiki/FAQ "wikilink"). Check there first** for answers to
your questions.

The Linuxwacom FAQ is based on the questions that we receive in email.
If you have a question that is not answered on the LWP FAQ or here, send
your email directly to the [mailing
list](/wiki/Main_Page#Mailing_lists "wikilink").

How do I get the special features of my stylus to work?
-------------------------------------------------------

See the LWP's
[FAQ](/wiki/FAQ#How_do_I_get_the_special_features_of_my_stylus_to_work? "wikilink")

I keep losing my wacomcpl (.xinitrc) settings. What can I do?
-------------------------------------------------------------

If you are losing your settings due to the screen saver or suspend you
could try cyberfish's screen saver, suspend/resume *monitor\_wacom* C
daemon. It uses HAL monitor's notification of DBus. Both it and Steven
L. Seed's modification of *monitor\_wacom* for hot plugging are posted
on this [linuxwacom-devel
thread](https://sourceforge.net/mailarchive/forum.php?thread_name=20110127060441.GA7826%40barra.i.followtheflow.org&forum_name=linuxwacom-devel).

**Note:** the LWP does not support these work arounds. The preference is
for Desktops to integrate these functions. No guarantees are made.

Is hot plug supported for X intput devices?
-------------------------------------------

See the LWP's
[FAQ](/wiki/FAQ#Is_hot_plugging_supported_for_my_USB_tablet? "wikilink")

A workaround for Xorg's that do not support hot plugging by Aristeu
Rozanski's is *wdaemon* linked at the bottom of the [Tablet
Hotplugging](/wiki/Linuxwacom_HOWTO#Hotplugging_the_device "wikilink") section
in the [Linuxwacom HOWTO](/wiki/Linuxwacom_HOWTO "wikilink"). Last development
on it was 9/08 so later models aren't included. This daemon is not
supported by the LWP and no guarantees are made.

Is my tablet supported? What about my special stylus?
-----------------------------------------------------

Please see the [Devices supported
table](/wiki/FAQ#Which_devices_are_supported? "wikilink") on the LWP FAQ. The
Main page also contains a link to the table of all tablets that are
known to be supported.

Who is responsible for this code?
---------------------------------

We are. That is, Linux users with Wacom tablets are responsible for the
development of this code. John Joganic took up the code in November of
2002 and created this project. The source code was written by at least
two dozen individuals, most of them are not directly involved in the
project any more. I, Ping Cheng, have assumed the project administrator
since 2003. What I ask of you, is to use the drivers and report the
problems you encounter.

Why does my cursor move randomly or stay at the top-left corner?
----------------------------------------------------------------

If you use a USB tablet, it is most probably because you didn't
configure the X driver properly. Please refer to [Configuring
X11](/wiki/Linuxwacom_HOWTO#Configuring_X11 "wikilink") for solutions.

If it happens to a serial tablet, please post your problem at [general
discussion](https://lists.sourceforge.net/lists/listinfo/linuxwacom-discuss).

<center>
<small> Copyright (C) 2002-2011 - LinuxWacom

This website and its contents are licensed under the GNU GENERAL PUBLIC
LICENSE. </small>

</center>
