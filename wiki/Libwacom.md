---
title: Libwacom
permalink: wiki/Libwacom/
layout: wiki
tags:
 - DeveloperPages
---

Libwacom is a new library to help implement Wacom tablet settings. It is
intended to be used by client-programs that need model identification.
It is already being used by the gnome-settings-daemon and the GNOME 3.4
Control Center Wacom tablet applet. In the future, the
[xf86-input-wacom](xf86-input-wacom "wikilink") driver may use it as
well.

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

The definition file **intuos4-8x13.tablet** demonstrates a "completed"
one:

    # Wacom
    # Intuos4 Large
    # PTK-840
    #
    # Button Map:
    # (A=1, B=2, C=3, ...)
    #
    #    *-----------------------*
    #    |                       |
    #  B |                       |
    #  C |                       |
    #  D |                       |
    #  E |                       |
    #  A |        TABLET         |
    #  F |                       |
    #  G |                       |
    #  H |                       |
    #  I |                       |
    #    |                       |
    #    *-----------------------*
    #
    # LED Map:
    # (XY=Bank X, LED Y)
    #
    #     *-----------------------*
    #     |                       |
    #  00 |                       |
    #  01 |                       |
    #  02 |        TABLET         |
    #  03 |                       |
    #     |                       |
    #     *-----------------------*
    #

    [Device]
    Name=Wacom Intuos4 8x13
    DeviceMatch=usb:056a:00ba
    Class=Intuos4
    Width=13
    Height=8
    Styli=0x802;0x80c;0x804;0x80a;0x40802;0x4080a;0x902;0x90a;0x20802;0x806;0x006;

    [Features]
    Reversible=true
    Stylus=true
    Ring=true
    Buttons=9
    BuiltIn=false

    [Buttons]
    Left=A;B;C;D;E;F;G;H;I

    OLEDs=B;C;D;E;F;G;H;I

    Ring=A
    RingNumModes=4

Submit your [git patch](/wiki/Submitting_Patches "wikilink") to
[linuxwacom-devel](https://lists.sourceforge.net/lists/listinfo/linuxwacom-devel).

Git Repository and Tar Files
----------------------------

The git repository is [located
here](https://sourceforge.net/p/linuxwacom/libwacom/ci/master/tree/). To
clone the git repository use: git clone
<git://git.code.sf.net/p/linuxwacom/libwacom> The tar files are [located
here](http://sourceforge.net/projects/linuxwacom/files/libwacom/).

Maintainer
----------

Bastien Nocera is the current libwacom maintainer.

Please do not send email regarding bugs or feature requests to the
maintainer. Use the [mailing lists](mailing_lists "wikilink") instead.

Dependencies
------------

Before building from git with ./autogen.sh, install the dependencies:

On Ubuntu:

<code>

sudo apt-get build-dep libwacom

or

sudo apt-get install libgudev-1.0-dev libxml++2.6-dev </code>

On Fedora:

`sudo yum-builddep libwacom`

<b>or</b>

`sudo dnf builddep libwacom`

On Centos 6:

`sudo yum install libgudev1-devel`

Building
--------

Depending on your system, you may find it helpful to use these commands

`./autogen.sh --prefix=/usr --libdir=/lib64`  
`make`  
`sudo make install`  

On Ubuntu 16.04 instead use:

`./autogen.sh --prefix=/usr --libdir=/usr/lib/x86_64-linux-gnu/`  

for the first command in the above block.

Data Files
----------

After building, copy the libwacom data files in to /usr/share/libwacom:

<code>

`sudo cp -r /home/`<path>`/libwacom/data/* /usr/share/libwacom/`

</code> or <code>

`sudo cp -r /home/`<path>`/linuxwacom-libwacom/data/* /usr/share/libwacom/`

</code>

Troubleshooting
---------------

You can check the installation with

<code>

`$ libwacom-list-local-devices`

</code>

This command may give an error:

<code>

`$ libwacom-list-local-devices `  
`(libwacom-list-local-devices:2595): libwacom-CRITICAL **: Duplicate match of 'usb:056a:00d0' on device 'Wacom Bamboo Touch (2FG)'.`

</code>

If you get this error you need to delete the following files which
shipped with your distro from an earlier version of libwacom:

<code>

`$ cd /usr/share/libwacom`  
`$ sudo rm bamboo-pen.tablet bamboo-craft.tablet bamboo-2fg.tablet bamboo-2fg-6x8.tablet bamboo-2fg-4x5.tablet`

</code>

These files have duplicate VID:PIDs, and cause the above error.

Udev Rules (from the README)
----------------------------

If you are updating libwacom to support a new device, the device should
also be added to the udev rule to ensure all required properties are
set.

When building from source generate an updated ruleset with
tools/generate-udev-rules after adding the tablet description files.

When updating an installed version of libwacom, add it manually to the
existing ruleset (/lib/udev/rules.d/65-libwacom.rules). Copy a similar
device and change the PID to match the PID from the device (for example
0357 below). Obtain the PID from the lsusb command in a terminal. Note
that the first line below should start with a '\#' to escape the line,
not '&\#'.

`&# Wacom Intuos Pro 2 M`  
`ENV{ID_BUS}=="usb", ENV{ID_VENDOR_ID}=="056a", ENV{ID_MODEL_ID}=="`**`0357`**`", ENV{ID_INPUT}="1", ENV{ID_INPUT_JOYSTICK}="", ENV{ID_INPUT_TABLET}="1"`  
`ATTRS{name}=="* Finger", ENV{ID_BUS}=="usb", ENV{ID_VENDOR_ID}=="056a", ENV{ID_MODEL_ID}=="`**`0357`**`", ENV{ID_INPUT_TOUCHPAD}="1"`  
`ATTRS{name}=="* Pad", ENV{ID_BUS}=="usb", ENV{ID_VENDOR_ID}=="056a", ENV{ID_MODEL_ID}=="`**`0357`**`", ENV{ID_INPUT_TABLET_PAD}="1"`  

You can check if your device was added properly in the rules by
libwacom-list-local-devices.
