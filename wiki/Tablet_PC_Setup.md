---
title: Tablet PC Setup
permalink: wiki/Tablet_PC_Setup/
layout: wiki
tags:
 - HOWTO
---

This page is for general tablet pc issues & tips as well as Brand and
model specific issues & tips.

The linuxwacom project welcomes additions to its mediawiki so please
join in if you have contributions.

Be aware the Wacom drivers are rapidly evolving and some of this
information may become dated. If so feel free to update and correct the
mediawiki.

Fujitsu tablet pc's
-------------------

**Tip** If your Wacom digitizer does not initialize (respond) on boot
many have found restarting X gets it working.

Calibration of Your Tablet PC
-----------------------------

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

Screen Rotation
---------------

See [Tablet PC Rotation
Script](/wiki/Rotation#Tablet_PC_Rotation_Script "wikilink").

### Tablet PC Automatic Rotation Script

The following script was developed for HP tablet pc's. Lenovo ThinkPads
(and very likely other tablet pc's) should be able to use it too with a
minor modification.

Determine your "Device names" with 'xinput list' entered in a terminal.
Then substitute them for stylus, eraser, and touch (if you have it) in
the following shell script using the quotes around the "Device names".
Only use the [xsetwacom](xsetwacom "wikilink") commands for the devices
you have.

Notice the script assumes you have
[CellWriter](/wiki/External_applications#CellWriter "wikilink") installed. You
can substitute the onscreen keyboard of your choice or comment out or
remove the lines.

    #!/bin/bash

    old="0"
    while true; do
        if [[ -e /sys/devices/platform/hp-wmi/tablet ]]; then
            new=`cat /sys/devices/platform/hp-wmi/tablet`
            if [[ $new != $old ]]; then
                if [[ $new == "0" ]]; then
                    echo "Rotate to landscape, hide CellWriter."
                    xrandr -o normal
                    xsetwacom set stylus rotate none
                    xsetwacom set eraser rotate none
                    xsetwacom set touch rotate none
                    cellwriter --hide-window
                elif [[ $new == "1" ]]; then
                    echo "Rotate to portrait, show CellWriter."
                    xrandr -o right
                    xsetwacom set stylus rotate cw
                    xsetwacom set eraser rotate cw
                    xsetwacom set touch rotate cw
                    cellwriter --show-window
                fi
            fi
            old=$new
            sleep 1s
        fi
    done

    # From Red_Lion post #576:  http://ubuntuforums.org/showthread.php?t=845911&page=58

Save it in "/home/yourusername/" as ".automagic\_rotation.sh" (without
the quotes), or whatever you want to name it. Make the file executable
and add it to your Startup Applications.

For the **ThinkPad** all you should need to do is change the lines.

        if [[ -e /sys/devices/platform/hp-wmi/tablet ]]; then
            new=`cat /sys/devices/platform/hp-wmi/tablet`

to

        if [[ -e /sys/devices/platform/thinkpad_acpi/hotkey_tablet_mode ]]; then
            new=`cat /sys/devices/platform/thinkpad_acpi/hotkey_tablet_mode`

Using these examples, if your tablet pc Brand & model also reports the
swivel hinge state, you should be able to modify the script to work for
you.

Also see [Magick
Rotation](/wiki/External_applications#Magick_Rotation "wikilink").

Touch Toggle Scripts
--------------------

Turning touch off can be convient when working in a graphics program, so
you don't have to worry about accidentally issuing a command, or worse,
drawing a line if you incidentally touch the tablet's screen when not
holding the stylus. Additionally some serial tablet pc's have issues
with extra lines being drawn in applications such as Xournal, when touch
is on while writing with the stylus.

The following script depends on the [xsetwacom](xsetwacom "wikilink")
get and set commands. The get command determines whether touch is on or
off while the two set commands turn touch on or off. The "device name"
for touch is found by using 'xsetwacom list' as mentioned in the script
comments.

    #!/bin/bash

    ## Get the "Device name" or ID number
    ## for touch from 'xsetwacom list'

    TOUCH_STATE=`xsetwacom get "Wacom ISDv4 Finger touch" touch`
    if [ "$TOUCH_STATE" == "on" ]
      then
        echo "Touch is ON, turning OFF."
        xsetwacom set "Wacom ISDv4 Finger touch" touch off
      else
        echo "Touch is OFF, turning ON."
        xsetwacom set "Wacom ISDv4 Finger touch" touch on
    fi

Enter the script in a file called .toggle-touch.sh (or whatever you
want) and place it in your home directory. Remember it will be a hidden
file because of the . in front. Also please remember you have to chmod
it to make it executable, or right click on it and make it executable
through Properties. Then you can place it in a launcher or bind a key to
the script. If you drag the launcher into a panel it will execute with a
single click.

### N-trig tablet pc Script

N-Trigs can also have the issue of extra lines being drawn even when
using the stylus if touch is on. If you have the HP TX2z or Dell XT &
XT2 you likely have your touch on the evdev driver rather than the Wacom
driver. In that case the Touch Toggle Script above will not work for
you. You can use the following script modified for the evdev driver.
This script works in a different manner from the one above. When first
toggled it issues the xinput touch disable command (0) and then creates
a file called touch\_off in the /tmp directory. When toggled again it
checks for the presence of the touch\_off file and if present issues the
xinput touch enabled (1) command, followed by the deletion of the file.

    #!/bin/bash

    ## evdev touch toggle script
    ##
    ## Get the "Device name" or ID number
    ## for touch from 'xinput list'.

    if [ -f /tmp/touch_off ]; then
            xinput set-prop 'N-Trig Touchscreen' 'Device Enabled' 1
        rm -f /tmp/touch_off && exit 0
    else
            xinput set-prop 'N-Trig Touchscreen' 'Device Enabled' 0
        echo 1 > /tmp/touch_off && exit 0
    fi
