---
title: Waltop Tablet Set Up
permalink: wiki/Waltop_Tablet_Set_Up/
layout: wiki
tags:
 - HOWTO
---

This page is for general tablet issues & tips as well as model specific
issues & tips.

The linuxwacom project welcomes additions to its mediawiki so please
join in if you have contributions.

Be aware the Wacom drivers are rapidly evolving and some of this
information may become dated. If so feel free to update and correct the
mediawiki.

Set Up
------

If you have at least the 2.6.35 kernel, particularly the more recent
versions, you should be able to get your tablet working on the Wacom
driver. Patches have been submitted by Nikolai Kondrashov to the kernel
that should improve Waltop tablet compatibility with the Wacom driver
starting in the 2.6.36 or 2.6.37 kernels. Waltop tablets are often
re-branded and will carry another OEM's name. To verify that your
tablet/stylus is indeed identified as a WALTOP (and to determine the
identifier for the match line) enter in a terminal the following.

    xinput list

You should see WALTOP (capitalized) in the tablet/stylus line. If you
see another tablet name in the line you do not have a Waltop. Use the
version of Waltop you see as the key word in the match line for the
following usb snippet. Place the snippet in a new file you'll create in
/etc/X11/xorg.conf.d called 52-waltop-on-wacom.conf. The snippet should
look like the following.

    Section "InputClass"
        Identifier "Waltop on wacom class"
        MatchProduct "WALTOP"
        MatchDevicePath "/dev/input/event*"
        Driver "wacom"
    EndSection

And that is all that is needed. After a reboot your Waltop will now be
using the Wacom X driver
[xf86-input-wacom](xf86-input-wacom "wikilink"). Since it is a system
file you need root privileges. If your system has gnome and sudo
installed you can use:

    gksudo gedit /etc/X11/xorg.conf.d/52-waltop-on-wacom.conf

You would use whatever text editor and root privilege access your
distribution supplies of course.

You're using the /etc/X11/xorg.conf.d directory because that is where
user specific changes are suppose to go. The /usr/share/X11/xorg.conf.d
directory is for distribution configuration files and may be overwritten
by a distribution update. Using /etc/X11/xorg.conf.d avoids this
possibility.

It's possible additional changes to support your Waltop may have been
made since your distribution's default version of
[xf86-input-wacom](xf86-input-wacom "wikilink"). If your distribution
hasn't been updating it you may want to consider cloning it from the git
repository.

Calibration of Your Stylus
--------------------------

See the [Calibration HOWTO](/wiki/Calibration "wikilink").

Dual and Multi-Monitor Set Up
-----------------------------

See [Dual and Multi-Monitor Set
Up](/wiki/Dual_and_Multi-Monitor_Set_Up "wikilink").

Graphical Configuration GUI
---------------------------

Work is proceeding on a replacement for wacomcpl. See [Graphical
Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

Waltop Tablet Configuration
---------------------------

Please see the [Tablet Configuration
HOWTO](/wiki/Tablet_Configuration "wikilink"). Just concentrate on the
material discussing the stylus, which is fairly brief.
