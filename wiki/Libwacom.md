---
title: Libwacom
permalink: wiki/Libwacom/
layout: wiki
tags:
 - DeveloperPages
---

Libwacom is a new library to help implement Wacom tablet settings. It is
intended to be used by both the xf86-input-wacom driver and
client-programs that need model identification. It is already being used
by the gnome-settings-daemon and the GNOME 3.4 Control Center Wacom
tablet applet.

Purpose
-------

Libwacom supplies metadata about Wacome tablets including whether or not
they are connected to your system, the list of styli it supports, as
well as information about the styli themselves. In the past this
information was only available within the drivers (as comments),
exported in different ways (sysfs attributes), non-machine readable in
public documentation, or, worst of all, hidden in Wacom's internal
drivers for OS X or Windows.

For example the Wacom tablet applet UI uses information exported by
libwacom such as:

-   whether the tablet is built-in (Cintiq or tablet PC) or external
-   which form factor it has and is it right or left-handed reversible
-   the list of styli it supports
-   for each stylus, its full name, the number of buttons, is there an
    eraser, what it looks like

After knowing what each tablet has to offer, a way to match the
definitions to XInput devices, assign settings per-tablet, and
importantly, switch stylus configuration when the user switches stylus
was needed. This is done using the new GsdWacomDevice and GsdWacomStylus
objects, shared between gnome-settings-daemon (which will apply the
configuration) and gnome-control-center (which will set the
configuration).

There are also a few debugging applications, such as list-wacom in
gnome-settings-daemon, to show the attached GsdWacomDevices, or
test-wacom in gnome-control-center, to test the display of tablets not
available to you.

Tablet Definition Files
-----------------------

If you have a Wacom tablet and it is not yet in libwacom/data, send us a
definition file for your tablet. This will assist applications in
configuring it because they can then access the metadata in libwacom
about your device. See the **wacom.example** file in libwacom/data for
explanations of the requested data and its format.

The definition file **intuos4-6x9-wl.tablet** demonstrates a "completed"
one:

    [Device]
    Name=Wacom Intuos 4 WL 6x9
    # FIXME add missing bluetooth match
    DeviceMatch=usb:056a:00bc
    Class=Intuos4
    Width=9
    Height=6
    Styli=0x802;0x804;0x40802;0x80c;0x80a;0x4080a;0x90a;0x20802;0x902;

    [Features]
    Reversible=true
    Stylus=true
    Ring=true
    VStrip=false
    HStrip=false
    Buttons=9
    BuiltIn=false

The **Name** is the parent device name exported from the kernel
(wacom\_wac.c) with *xinput list*, minus the Pen and Finger seen with
USB tablets that have touch. Be sure you are using a recent kernel, say
3.0 or up and not input-wacom's wacom.ko. Then to obtain the above file
name remove the Wacom and using small case string the name together with
hyphens and add the suffix **.tablet**.

Submit your [git patch](/wiki/Submitting_Patches "wikilink") to
[linuxwacom-devel](https://lists.sourceforge.net/lists/listinfo/linuxwacom-devel).

Git Repository and Tar Files
----------------------------

The git repository is [located
here](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/libwacom;a=summary).
To clone the git repository use: clone
<git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/libwacom> The
tar files are [located
here](http://sourceforge.net/projects/linuxwacom/files/libwacom/).

Contributors (Partial list)
---------------------------

Peter Hutterer made the original Wacom panel for GNOME 3.2, and the
first version of libwacom.

Jason Gerecke, from Wacom, who did most of the initial work on
calibration support for GNOME 3.4, is working on the related
display-mapping. This will allow choosing whether a tablet's working
area is the whole desktop, or a single monitor when in multiple monitors
are used.

Bastien Nocera (the libwacom maintainer) fixed the layout bugs in the
settings panel and is starting work on tablet button mapping. Soon
goodies like the LEDs on the tablet matching up with the selected
keyboard shortcut should be available!
