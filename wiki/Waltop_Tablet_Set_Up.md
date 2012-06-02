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

Starting with the 2.6.35 kernel some Waltop tablets were again supported
by the Wacom X driver, [xf86-input-wacom](xf86-input-wacom "wikilink").
Ongoing work on the kernel continues to add more Waltop models and
improve Waltop support, including compatibility with the Wacom X driver.
Nikolai Kondrashov from the DIGI*mend* project is involved in much of
this work.

Waltop tablets are often re-branded and will carry another OEM's name.
To verify that your tablet/stylus is indeed identified as a WALTOP (and
to check the keyword for the match line) enter in a terminal the
following.

    xinput list

You should see WALTOP (capitalized) in the tablet/stylus line(s). If you
see another tablet name in the line you do not have a Waltop.

### 52-waltop.conf

To use static options for your tablet's pen place the following snippet
in a new file you'll create in /etc/X11/xorg.conf.d called
**52-waltop.conf**. The snippet should look like the following.

    Section "InputClass"
        Identifier "Waltop pen"
        MatchProduct "WALTOP"
        MatchIsTablet "on"
        MatchDevicePath "/dev/input/event*"
        Driver "wacom"
            # Apply custom Options below.

    EndSection

The MatchIsTablet match is used so only the Waltop's digitizer/stylus is
matched, not its pad buttons. This is because the pad buttons are not
supported by xf86-input-wacom and should instead be on xf86-input-evdev.
Since it is a system file you need root privileges. If your system has
gnome and sudo installed you can use:

    gksudo gedit /etc/X11/xorg.conf.d/52-waltop-on-wacom.conf

You would use whatever text editor and root privilege access your
distribution supplies of course.

If your tablet has a multifunction dial (jog wheel) and you would like
to enable the scroll function see
[52-waltop.conf](http://sourceforge.net/apps/mediawiki/digimend/index.php?title=Tablet_setup_with_xf86-input-wacom#52-waltop.conf)
on the DIGI*mend* mediawiki site.

You're using the /etc/X11/xorg.conf.d directory because that is where
user specific changes are suppose to go. The /usr/share/X11/xorg.conf.d
directory is for distribution configuration files and may be overwritten
by a distribution update. Using /etc/X11/xorg.conf.d avoids this
possibility.

It's possible additional changes to support your Waltop may have been
made since your distribution's default version of xf86-input-wacom. If
your distribution hasn't been updating it you may want to consider
cloning it from the git repository.

Calibration of Your Stylus
--------------------------

See the [Calibration HOWTO](/wiki/Calibration "wikilink").

Dual and Multi-Monitor Set Up
-----------------------------

See [Dual and Multi-Monitor Set
Up](/wiki/Dual_and_Multi-Monitor_Set_Up "wikilink").

Graphical Configuration GUI
---------------------------

Several are now available, see [Graphical Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

Waltop Tablet Configuration
---------------------------

Please see the [Tablet Configuration
HOWTO](/wiki/Tablet_Configuration "wikilink"). Just concentrate on the
material discussing the stylus, which is fairly brief.

DIGI*mend* project
------------------

Further information on Waltop tablets is available at the [DIGI*mend*
project](https://sourceforge.net/apps/mediawiki/digimend/index.php?title=DIGImend)
site. This includes Waltop USB kernel driver patches for tablet support
on older kernels and using the xf86-input-evdev instead of
xf86-input-wacom as the X driver.
