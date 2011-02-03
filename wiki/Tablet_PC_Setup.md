---
title: Tablet PC Setup
permalink: wiki/Tablet_PC_Setup/
layout: wiki
tags:
 - HOWTO
---

This page is for general tablet pc issues & tips as well as Brand and
model specific issues & tips.

Fujitsu tablet pc's
-------------------

**Tip** If your Wacom digitizer does not initialize (respond) on boot
many have found restarting X gets it working.

N-trig tablet pc's
------------------

If you have the HP TX2z or Dell XT & XT2 you likely have your touch on
the evdev driver rather than the Wacom driver. In that case the standard
Touch Toggle Script will not work for you. You can use the following
script modified for the evdev driver.

### Evdev Touch Toggle Script

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

Screen Rotation
---------------

See [Tablet PC Rotation
Script](/wiki/Rotation#Tablet_PC_Rotation_Script "wikilink").

Touch Toggle Script
-------------------

The following script depends on the [xsetwacom](xsetwacom "wikilink")
get and set commands. The "Device name" for touch is found by using
'xinput list' as mentioned in the script comments.

    #!/bin/bash

    ## Get the "Device name" or ID number
    ## for touch from 'xinput list'.

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
