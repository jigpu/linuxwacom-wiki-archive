---
title: Wacom Tablet Set Up
permalink: wiki/Wacom_Tablet_Set_Up/
layout: wiki
tags:
 - HOWTO
---

This page details the general process of setting up your tablet to your
needs. Many of the steps will have links to more detailed instructions
if you are interested in knowing more.

Configuration of Your Tablet
----------------------------

The first step in setting up your tablet is to get the general
configuration set according to your liking. The tablet's initial
configuration is dictated by configuration files, and may be changed
on-the-fly with configuration utilities.

### Default Configuration

Under a modern X server using udev for hotplugging,
[xorg.conf.d](xorg.conf.d "wikilink") is responsible for defining a
tablet's default settings. Older servers may use
[HAL](/wiki/Configuring_X#Hotplugging_With_HAL "wikilink"), and in rare cases
an [xorg.conf](/wiki/Configuring_X#Static_xorg.conf_Setup "wikilink") file is
necessary. Regardless of which backend performs the setup, these are the
ideal place to set system-wide defaults. For example, one could adjust
the default rotation property for a Cintiq or TabletPC which is often
used in a particular orientation. Perhaps you have a lot of
electromagnetic interference near your workstation and feel that
increasing the default "Suppress" value for all tablets would be useful.
Developers may prefer to have [debug
information](/wiki/Debugging#Enabling_in-driver_debugging_statements "wikilink")
turned up by default, in order to log the events which occur as X starts
up.

### Run-Time Configuration

Our driver includes the command-line driven
[xsetwacom](xsetwacom "wikilink") utility which is capable of modifying
the configuration of your tablet on-the-fly. This can be useful for
allowing per-user settings, temporarily adjusting the current settings,
or gaining an understanding of how particular options affect the
tablet's operation.

For example, suppose you wish to have the ExpressKeys perform certain
keyboard actions to make image manipulation in GIMP easier. Your
particular preference for ExpressKey behavior may not be the same as
another user using the same workstation. By calling xsetwacom in a login
script, you can customize the system-wide defaults to your own liking.
As another example, suppose that you feel GIMP's paintbrush needs a
more-firm feel and the airbrush a less-firm feel. By using xsetwacom,
you can change the PressureCurve property of your stylus on-the-fly as
you swap back and forth between paintbrush and airbrush.

### Application Configuration

[Third-party applications](external_applications "wikilink") may also
benefit from configuration. For example, some GTK-based applications
such as GIMP and Inkscape require that you enable your tablet as an
"extended input device" before they will use data like pressure. You may
also wish to configure certain tablet buttons to run programs; while
xsetwacom is incapable of this, window managers like Compiz may provide
the necessary options.

Dual and Multi-Monitor Set Up
-----------------------------

For systems set up with multiple monitors, the X server maps the
tablet's area to the entire desktop by default. Oftentimes this is the
desired behavior, but in some cases (particularly for TabletPCs or
Cintiqs) you will want to have the stylus be mapped to a single output.
To do this, X must be notified to change its tablet-to-display mapping.
The xsetwacom utility includes a handy "MapToOutput" parameter which
allows you to specify which particular output you would like the tablet
to be mapped to. To use find the names of available outputs, you can use
the `xrandr` command.

While the legacy [linuxwacom](linuxwacom "wikilink") driver supports the
TwinView extension used by the binary "nvidia" driver, the new
[xf86-input-wacom](xf86-input-wacom "wikilink") driver had TwinView
support added with the 0.11.99.1 release. Earlier versions of
xf86-input-wacom relied on RandR and were not compatible with TwinView.
Rather than using the xsetwacom "MapToOutput" parameter it is still
possible to manually calculate and notify the X server of the correct
tablet-to-display mapping with those earlier versions of
xf86-input-wacom. See [Dual and Multi-Monitor Set
Up](/wiki/Dual_and_Multi-Monitor_Set_Up "wikilink") for additional details.

Calibration of Your Tablet
--------------------------

Calibration is necessary when the pointer on the screen does not
coincide with where the input tool is located on the tablet. This is
usually most noticeable on screen-mounted tablets (e.g. Tablet PCs or
Cintiqs), often after rotation. There can be multiple causes of
misalignment, though [parallax](http://en.wikipedia.org/wiki/Parallax)
from your working position is the most common cause. "Opaque" tablets
also may suffer to a less noticible extent when they have a different
aspect ratio than the display. This particular kind of miscalibration
results in drawn cicles appearing on-screen as ellipses.

The easiest way to calibrate a screen-mounted tablets like a TabletPC or
Cintiq is to use the 3rd-party
[xinput-calibrator](external_applications "wikilink") utility. By
tapping the displayed crosshairs while holding your stylus normally and
in your normal working position (so the utility can correct for
parallax), it will calculate the necessary modifications. It should
provide you with a set of four numbers corresponding to the top-left and
bottom-right coordinates of the tablet. These numbers can then be fed
into xsetwacom's "Area" parameter to be applied.

For opaque tablets suffering from aspect ratio mismatch, you will need
to determine the tablet's starting Area from xsetwacom and then
calculate the necessary reduction so the Area's aspect ratio matches
your desktop.

See the [calibration](calibration "wikilink") page for more information.

Tablet-Specific Suggestions
---------------------------

Any sort of configuration suggestions which are fairly tablet-specific
may appear here.

### Bamboo P&T Touch Toggle Script

Turning touch off helps to avoid accidental input if you touch the
tablet when not holding the stylus (it also duplicates the function and
button assignment in Windows).

The following script requires [xsetwacom](xsetwacom "wikilink"). The
device name for touch is found by using *xsetwacom list dev* as
mentioned in the script comments.

    #!/bin/bash

    ## Get the "Device name" or ID number
    ## for touch from 'xsetwacom list dev'

    DEVICE="Wacom BambooFun 2FG 4x5 Finger touch" 
    TOUCH_STATE=`xsetwacom get "$DEVICE" touch`
    if [ "$TOUCH_STATE" == "on" ]
      then
        echo "Touch is ON, turning OFF."
        xsetwacom set "$DEVICE" touch off
      else
        echo "Touch is OFF, turning ON."
        xsetwacom set "$DEVICE" touch on
    fi

Place the script in a file called *bin/toggle-touch.sh* in your home
directory. Make the script executable with *chmod +x
$HOME/bin/toggle-touch.sh*. By binding a tablet button to execute the
script (likely through your window manager) you can easily toggle touch
on and off.

### Intuos4 LED and OLED

Eduard Hasenleithner's patch set for LED and OLED support [Input: wacom
- add Intuos4 LED and OLED
control](https://github.com/dtor/input/commit/5d7e7d479856f23eebc272128905a7ecada367fb)
has been accepted into the 3.2 kernel. Binary sysfs attributes are used
to provide manipulation of the LEDs and OLEDs. Also posted on
linuxwacom-devel is a [backport for earlier
kernels](http://sourceforge.net/mailarchive/forum.php?thread_name=CAF8JNhLzLkFfc6fnORxnZ_V-T4UUKbTr0SsU8AyqxWpS4DNAsQ%40mail.gmail.com&forum_name=linuxwacom-devel)
which has been committed to the [input-wacom](input-wacom "wikilink")
git repository. He has begun work implementing support for the new sysfs
"wacom\_led" attribute group through xf86-input-wacom's xsetwacom.

Impatient users have already applied the patch set and compiled their
own custom wacom.ko. They report they can choose which one of the 4
small LEDs to light up, make the intensity of the LED change when the
stylus is touching the tablet, and send images to the OLEDs.

### DTU-1141

The DTU-1141 relies on both the [Wacom Kernel
Driver](http://linuxwacom.sourceforge.net/wiki/index.php/Input-wacom)
and [Display Link's Linux software
package](http://www.displaylink.com/downloads/ubuntu.php). Directions
for installing DisplayLink's package are available at DisplayLink's
site. DisplayLink has prepared their package to be compatible with
Ubuntu 14.04.2 LTS. We have verified that the DTU-1141 works as both a
second display and a standalone monitor with an up to date [Ubuntu
14.04.02](http://old-releases.ubuntu.com/releases/).
