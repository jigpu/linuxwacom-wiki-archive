---
title: Wacom Tablet Set Up
permalink: wiki/Wacom_Tablet_Set_Up/
layout: wiki
tags:
 - HOWTO
---

This page is for general tablet issues & tips as well as model specific
issues & tips.

The LWP project welcomes additions to its mediawiki so please join in if
you have contributions.

Be aware the Wacom drivers are rapidly evolving and some of this
information may become dated. If so feel free to update and correct the
mediawiki.

Bamboo Pen & Touch
------------------

In October of 2009 Wacom released a new series of 5 tablets we'll call
generically the Bamboo Pen & Touch's. The Bamboo Pen had a stylus (with
no eraser), the Bamboo Touch had 2FG touch only, and the Bamboo Pen &
Touch's came with a stylus/eraser and touch(2FG)/pad(4 tablet buttons).
This was followed in October of 2010 by the release of 5 new Bamboo Pen
& Touch's with two of them being "Special Editions" with 4FGT.

To determine your product ID enter in a terminal 'lsusb' and look in the
Wacom line. The first 5 models are the 0xD0, 0xD1, 0xD2, 0xD3, & 0xD4.
The new models are the 0xD6, 0xD7, 0xD8, 0xDA, & 0xDB. See [Device
IDs](/wiki/Device_IDs "wikilink").

### Setup

Depending on your distribution's kernel the 5 original models may not be
recognized because the default wacom.ko with the kernel may not have the
models in it. If your distribution has not backported support, and has
no plans to do so, it probably is a better idea to wait for a release
that supports your tablet. However if you feel you can't wait and must
be able to use your BambooPT now you could use linuxwacom-0.8.8-10's
wacom.ko as it does have the models in it. Installing out-of-tree kernel
patches is never a good idea, so give this some thought before
proceeding. If you decide to proceed see
[Linuxwacom](/wiki/Linuxwacom "wikilink").

Note the location of the newly compiled wacom.ko will appear in the
output after the ./configure line under the *BUILD OPTIONS:* header.
Just copy it into place.

Keeping the cavets about out-of-tree kernel patches mentioned above in
mind, the newer 5 models are supported by
[input-wacom](input-wacom "wikilink")-0.10.10-2, which has all 10 of the
models added.

All 10 models have been added to
[xf86-input-wacom-0.10.10+](xf86-input-wacom "wikilink") and later as of
January 21, 2011.

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
$HOME/bin/toggle-touch.sh*. Then you have to make a key binding for it
in order to assign it to a tablet button. This depends on your window
manager.

### Compiz

If your window manager is compiz, install the CompizConfig Settings
Manager (if you haven't already).

We are attempting the following setup: when you press button 1, you
toggle between touch on or off.

The setup for this is a bit roundabout though:

-   configure the tablet to send a key combination when button 1 is
    pressed.
-   the key combination triggers the window manager to run the script
    above
-   the script then toggles the button

In the configuration example below, we will use button 1 and the key
combination of Ctrl+T. If you chose a different button or key
combination, replace the occurances accordingly.

To configure all this, do the following:

-   first, configure the tablet to send the key combination

<!-- -->

    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" Button1 "key ctrl t"

-   then, in the CompizConfig Settings Manager, click on the General
    option
-   click on Commands
-   in Command line 0 (or whatever line you want) add the path to
    toggle-touch.sh, e.g.: */home/yourusername/bin/toggle-touch.sh*
-   click on Key Bindings and then to edit the corresponding Run command
    0 click on the Disabled button.
-   press the Grab key combination button and then press the key
    combination
-   tablet button 1 should now toggle the touch between on and off

Intuos4
-------

### Intuos4 Wireless

Unfortunately the Intuos 4 Wireless is not currently supported by the
Bluez stack. It is not simply a matter of adding the Vendor ID 0x56a and
Product ID 0xBD alongside the Graphire Bluetooth Wireless' 0x56a & 0x81.
This is because the Graphire Bluetooth Wireless is a Protocol 4 device
(tool serial numbers are not reported) while the Intuos 4 Wireless is
Protocol 5 (serial numbers reported). So code for Protocol 5 support
needs to be added to the Bluez stack. In the meantime you will need to
use your usb "charging" cable for your tablet to work in linux.

### OLED's

Content Pending.

Calibration of Your Tablet
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
