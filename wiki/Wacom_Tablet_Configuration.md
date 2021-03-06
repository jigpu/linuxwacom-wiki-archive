---
title: Wacom Tablet Configuration
permalink: wiki/Wacom_Tablet_Configuration/
layout: wiki
---

This page is for model specific configuration issues & tips.

The LWP project welcomes additions to its mediawiki so please join in if
you have contributions.

Currently [xf86-input-wacom](xf86-input-wacom "wikilink") does not have
a configuration gui like linuxwacom's Wacom Control Panel (wacomcpl).
However since **wacomcpl** stored its settings as a
[xsetwacom](xsetwacom "wikilink") script in **.xinitrc** we can at least
duplicate the .xinitrc, calling it say **.xsetwacom.sh**. **These are a
temporary hack** while we wait on a replacement for wacomcpl, see
[Graphical Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

Below is a set of sample scripts for various tablets. If yours is not
among them noticed there is a high degree of overlap. They're all Wacom
tablets after all. So it should be relatively straight forward to
assemble one for your tablet. While doing things this way may seem a
little strange at first you'll find yourself quickly adapting. And this
allows you some of the finer control over your tablet's features
wacomcpl gave you.

**Note** While these xsetwacom commands were working as of 2-1-11 be
aware that the xsetwacom interface may change without notice. If this
happens feel free to update and correct this LWP mediawiki page.

If a xsetwacom command stops working your friend is the get command.

Installing the Script
---------------------

While you can change your settings on the fly using xsetwacom commands
in a terminal they only apply during the current session. So you need to
set it up to auto-start. Create a file and copy & paste the contents of
the script you want into it. Rename it .xsetwacom.sh (or whatever you
want) and place it in your home directory. Remember it will be a hidden
file because of the . in front. To enable the xsetwacom commands in the
.xsetwacom.sh file to apply to Xserver through a reboot you enter in a
terminal.

    chmod +x ~/.xsetwacom.sh

Or you could right click on the file and in Properties, in the
Permission tab, check Execute as program. Then go to
System-&gt;Preferences-&gt;Startup Applications (or your distributions
equivalent) and click on Add and for the command write "sh
/home/yourusername/.xsetwacom.sh" (without the quotes).

Once the script is executable you can double click on it to apply it's
settings or reboot to check the auto-start set up.

The Sample Xsetwacom Scripts
----------------------------

The following scripts were authored by Favux. Where co-developed with
others they are cited in the relevant script. All or almost all of the
values are the defaults with the default stated and the appropriate
range given. The exception being the pad (tablet buttons). Examples are
shown of how to configure those for different uses in different
applications. The scripts are intended to provide you with a framework
to configure your settings in a text file rather than at the command
line, with similar results to what the wacomcpl gui would have produced.

In the example scripts below both "device name" and ID \# are used. Be
sure to check for yours using 'xinput list' (without the quotes) in a
terminal and use them. When you use a xorg.conf the "device names" will
be stylus, eraser, touch, and pad. If you are hot plugging your tablet
or other devices be sure to use "device name" as the ID \# can change.

### Bamboo

    ## Device names and ID numbers from 'xinput list' entered in a terminal.
    #
    ## In the example "Device name" not ID # is used.  Note if you use the
    ## xorg.conf the "Device names" will be stylus, eraser, touch, and pad.
    #
    ## If you are hot plugging use "Device name" as ID # can change.
    #
    ## ClickForce changes name to Threshold with xf86-input-wacom 0.10.9 (11-19-10)
    #
    ## Warning:  Changing Mode to either Absolute or Relative in stylus/eraser stops
    ## the mouse from being able to pull guidelines out of the ruler in Gimp.

    ## stylus = "Wacom BambooFun 4x5 stylus" = ID 16
    xsetwacom set "Wacom BambooFun 4x5 stylus" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom BambooFun 4x5 stylus" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    xsetwacom set "Wacom BambooFun 4x5 stylus" ClickForce "27"  # pressure, default is 27, range is 0-2047
    #xsetwacom set "Wacom BambooFun 4x5 stylus" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom BambooFun 4x5 stylus" PressCurve "5 10 90 95" # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom BambooFun 4x5 stylus" TPCButton "on"  # stylus tip + button, or "off" for hover mode
    #xsetwacom set "Wacom BambooFun 4x5 stylus" Mode "Absolute"  # or Relative
    xsetwacom set "Wacom BambooFun 4x5 stylus" Button1 "Button 1"  # left mouse click
    xsetwacom set "Wacom BambooFun 4x5 stylus" Button2 "Button 2"  # right mouse click
    xsetwacom set "Wacom BambooFun 4x5 stylus" Button3 "Button 3"  # middle mouse click

    ## eraser = "Wacom BambooFun 4x5 eraser" = ID 13
    xsetwacom set "Wacom BambooFun 4x5 eraser" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom BambooFun 4x5 eraser" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    xsetwacom set "Wacom BambooFun 4x5 eraser" ClickForce "27"  # pressure, default is 27, range is 0-2047
    #xsetwacom set "Wacom BambooFun 4x5 stylus" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom BambooFun 4x5 eraser" PressCurve "0 10 90 100" # Bezier curve, default is 0,0,100,100
    #xsetwacom set "Wacom BambooFun 4x5 eraser" Mode "Absolute"  # or "Relative" cursor movement
    xsetwacom set "Wacom BambooFun 4x5 eraser" Button1 "Button 1"

    ## cursor = "Wacom BambooFun 4x5 cursor" = ID 14
    xsetwacom set "Wacom BambooFun 4x5 cursor" Suppress "4"  # data pt.s trimmed, 0-100, default is 4, 0-20
    xsetwacom set "Wacom BambooFun 4x5 cursor" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    xsetwacom set "Wacom BambooFun 4x5 cursor" CursorProx "10"  # proximity in/out value, default is 10?, range?
    #xsetwacom set "Wacom BambooFun 4x5 cursor" Mode "Relative"  # or Absolute
    xsetwacom set "Wacom BambooFun 4x5 cursor" Button1 "1"  # left mouse click
    xsetwacom set "Wacom BambooFun 4x5 cursor" Button2 "2"  # middle mouse click
    xsetwacom set "Wacom BambooFun 4x5 cursor" Button3 "3"  # right mouse click

    ## pad = "Wacom BambooFun 4x5 pad" = ID 15
    xsetwacom set "Wacom BambooFun 4x5 pad" Button1 "key Esc"
    xsetwacom set "Wacom BambooFun 4x5 pad" Button2 "key F12 "
    xsetwacom set "Wacom BambooFun 4x5 pad" AbsWDn "key CTRL SHIFT s"
    xsetwacom set "Wacom BambooFun 4x5 pad" AbsWUp "key CTRL SHIFT s"
    xsetwacom set "Wacom BambooFun 4x5 pad" Button3 "key KP_Next"
    xsetwacom set "Wacom BambooFun 4x5 pad" Button4 "key KP_Prior"

    ## Developed with Patrick Horgan @ Ubuntu forums

### Bamboo Pen & Touch

    ## Device names and ID numbers from 'xinput list' entered in a terminal.
    #
    ## In the example both "Device name" and ID # are used.  Note if you use the
    ## xorg.conf the "Device names" will be stylus, eraser, touch, and pad.
    #
    ## If you are hot plugging use "Device name" as ID # can change.
    #
    ## ClickForce changes name to Threshold with xf86-input-wacom 0.10.9 (11-19-10)
    #
    ## Warning:  Changing Mode to either Absolute or Relative in stylus/eraser stops
    ## the mouse from being able to pull guidelines out of the ruler in Gimp.

    ## stylus = ID 9 = "Wacom BambooFun 2FG 4x5 Pen stylus"
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set 9 PressCurve "5 10 90 95"  # Bezier curve, default is 0,0,100,100
    xsetwacom set 9 TPCButton "on"  # stylus tip + button, or "off" for hover mode
    #xsetwacom set 9 Mode "Absolute"  # or "Relative" cursor movement
    xsetwacom set 9 Button1 "1"  # left mouse click
    xsetwacom set 9 Button2 "3"  # right mouse click
    xsetwacom set 9 Button3 "2"  # middle mouse click

    ## eraser = ID 8 = "Wacom BambooFun 2FG 4x5 Pen eraser"
    xsetwacom set 8 Suppress "4"  # data trimmed, default is 4, 0-20
    xsetwacom set 8 RawSample "2"  # default is 2, 0-100
    #xsetwacom set 8 ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set 8 Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set 8 PressCurve "0 10 90 100"  # Bezier curve, default is 0,0,100,100
    #xsetwacom set 8 Mode "Absolute"  # or "Relative" cursor movement
    xsetwacom set 8 Button1 "1"

    ## touch = ID 11 = "Wacom BambooFun 2FG 4x5 Finger touch"
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" Touch "on"  # or "off"
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" Gesture "on"  # or "off"
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" Suppress "4"  # data trimmed, default is 4, 0-20
    xsetwacom set 11 RawSample "2"  #  default is 2, 0-100
    #xsetwacom set 11 ClickForce "27"  # default is 27, range is 0-2047
    xsetwacom set 11 Threshold "27"  # default is 27, range is 0-2047
    xsetwacom set 11 Mode "Relative"
    # 1FG dbl. tap is left click, 2FG dbl. tap is right click
    xsetwacom set 11 ZoomDistance "50"  # default is 50
    xsetwacom set 11 ScrollDistance "20"  # default is 20
    xsetwacom set 11 TapTime "250"  # 2FG R click, default is 250 ms

    ## pad = ID 10 = "Wacom BambooFun 2FG 4x5 Finger pad"
    xsetwacom set 10 Button1 "key ctrl t"  # toggle touch script
    xsetwacom set 10 Button2 "key backspace"
    xsetwacom set 10 Button3 "3"  # right mouse click
    xsetwacom set 10 Button4 "key alt left"  # Back a page in FireFox

    ## Developed with dr4ziw and iRi_E @ Ubuntu forums

### Cintiq

    ## Device names and ID numbers from 'xinput list' entered in a terminal.
    #
    ## In the example "Device name" not ID # is used.  Note if you use the
    ## xorg.conf the "Device names" will be stylus, eraser, touch, and pad.
    #
    ## If you are hot plugging use "Device name" as ID # can change.
    #
    ## ClickForce changes name to Threshold with xf86-input-wacom 0.10.9 (11-19-10)
    #
    ## Warning:  Changing Mode to either Absolute or Relative in stylus/eraser stops
    ## the mouse from being able to pull guidelines out of the ruler in Gimp.

    ## stylus = ID 12 = "Wacom Cintiq 21UX2 stylus"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom Cintiq 21UX2 stylus" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" ClickForce "27" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Cintiq 21UX2 stylus" Threshold "27" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Cintiq 21UX2 stylus" PressCurve "0 25 75 100"  # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom Cintiq 21UX2 stylus" TPCButton "on"  # stylus tip + button, or "off" for hover mode
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" Mode "Absolute"  # or "Relative" cursor movement
    xsetwacom set "Wacom Cintiq 21UX2 stylus" Button1 "Button 1"  # left mouse click
    xsetwacom set "Wacom Cintiq 21UX2 stylus" Button2 "Button 2"  # right mouse click
    xsetwacom set "Wacom Cintiq 21UX2 stylus" Button3 "Button 3"  # middle mouse click
    # default coordinates in /var/log/Xorg.0.log
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" topx "0"
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" topy "0"
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomx "87200"
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomy "65600"
    # sample coordinates
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" topx "-20"
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" topy "12"
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomx "87064"
    #xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomy "65691"

    ## eraser = ID 9 = "Wacom Cintiq 21UX2 eraser"
    xsetwacom set "Wacom Cintiq 21UX2 eraser" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom Cintiq 21UX2 eraser" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Cintiq 21UX2 eraser" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Cintiq 21UX2 eraser" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Cintiq 21UX2 eraser" PressCurve "0 25 75 100"  # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom Cintiq 21UX2 eraser" TPCButton "on"  # stylus tip + button, or "off" for hover mode
    #xsetwacom set "Wacom Cintiq 21UX2 eraser" Mode "Absolute"  # or "Relative" cursor movement
    xsetwacom set "Wacom Cintiq 21UX2 eraser" Button1 "Button 1"
    # coordinates are the same as used for the stylus
    #xsetwacom set "Wacom Cintiq 21UX2 eraser" topx "-20"
    #xsetwacom set "Wacom Cintiq 21UX2 eraser" topy "12"
    #xsetwacom set "Wacom Cintiq 21UX2 eraser" bottomx "87064"
    #xsetwacom set "Wacom Cintiq 21UX2 eraser" bottomy "65691"

    ## pad = ID 11 = "Wacom Cintiq 21UX2 pad"
    xsetwacom set "Wacom Cintiq 21UX2 pad" StripLDn "key -"  # zoom out
    xsetwacom set "Wacom Cintiq 21UX2 pad" StripLUp "key +"  # zoom in
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button1 "key r"  # rectangular selections
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button2 "key ctrl shift a"  # select none
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button3 "key p"  # paintbrush
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button4 "key Tab"  # toggle docks
    ## Need to figure out circle "button".
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button5 "key SHIFT CONTROL left"  # prev/next brush (must be mapped in GIMP)
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button6 "key SHIFT CONTROL right" 
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button7 "key CTRL SHIFT e"  # zoom to fit window
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button8 "key F11"  # fullscreen

    xsetwacom set "Wacom Cintiq 21UX2 pad" StripRDn "key ALT down"  # brush radius (must be mapped in GIMP)
    xsetwacom set "Wacom Cintiq 21UX2 pad" StripRUp "key ALT up"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button9 "key ctrl"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button10 "key alt"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button11 "key shift"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button12 "key tab"
    ## Need to figure out circle "button".
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button13 "key apostrophe"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button14 "key backspace"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button15 "key backslash"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button16 "key ctrl z"

    ## alternate pad button assignment examples
    #StripLDn "key Prior"  # page up
    #StripLUp "key Next"  # page down

    #StripRDn "key KP_Add"  # NumpadPlus
    #StripRUp "key KP_Subtract"  # NumpadMinus

    ## Developed with Jimmr & ewientje @ Ubuntu forums

### Intuos3

    ## Device names and ID numbers from 'xinput list' entered in a terminal.
    #
    ## In the example "Device name" not ID # is used.  Note if you use the
    ## xorg.conf the "Device names" will be stylus, eraser, touch, and pad.
    #
    ## If you are hot plugging use "Device name" as ID # can change.
    #
    ## ClickForce changes name to Threshold with xf86-input-wacom 0.10.9 (11-19-10)
    #
    ## Warning:  Changing Mode to either Absolute or Relative in stylus/eraser stops
    ## the mouse from being able to pull guidelines out of the ruler in Gimp.

    ## stylus = ID 13 = "Wacom Intuos3 6x8 stylus"
    xsetwacom set "Wacom Intuos3 6x8 stylus" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom Intuos3 6x8 stylus" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Intuos3 6x8 stylus" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos3 6x8 stylus" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos3 6x8 stylus" PressCurve "5 10 90 95"  # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom Intuos3 6x8 stylus" TPCButton "on"  # stylus tip + button, or "off" for hover mode
    xsetwacom set "Wacom Intuos3 6x8 stylus" Mode "Absolute"  # or Relative cursor movement
    xsetwacom set "Wacom Intuos3 6x8 stylus" Button1 "1"  # left mouse click
    xsetwacom set "Wacom Intuos3 6x8 stylus" Button2 "3"  # right mouse click
    xsetwacom set "Wacom Intuos3 6x8 stylus" Button3 "2"  # middle mouse click
    # default coordinates in /var/log/Xorg.0.log
    #xsetwacom set "Wacom Intuos3 6x8 stylus" Topx "0"
    #xsetwacom set "Wacom Intuos3 6x8 stylus" Topy "0"
    #xsetwacom set "Wacom Intuos3 6x8 stylus" Bottomx "40640"
    #xsetwacom set "Wacom Intuos3 6x8 stylus" Bottomy "30480"

    ## eraser = ID 10 = "Wacom Intuos3 6x8 eraser"
    xsetwacom set "Wacom Intuos3 6x8 eraser" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom Intuos3 6x8 eraser" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Intuos3 6x8 eraser" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos3 6x8 eraser" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos3 6x8 eraser" PressCurve "0 10 90 100"  # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom Intuos3 6x8 eraser" Mode "Absolute"  # or Relative cursor movement
    xsetwacom set "Wacom Intuos3 6x8 eraser" Button1 "1"
    # coordinates are the same as used for the stylus
    #xsetwacom set "Wacom Intuos3 6x8 eraser" Topx "0"
    #xsetwacom set "Wacom Intuos3 6x8 eraser" Topy "0"
    #xsetwacom set "Wacom Intuos3 6x8 eraser" Bottomx "40640"
    #xsetwacom set "Wacom Intuos3 6x8 eraser" Bottomy "30480"

    ## cursor = ID 11 = "Wacom Intuos3 6x8 cursor"
    xsetwacom set "Wacom Intuos3 6x8 cursor" Suppress "4"  # data pt.s trimmed, 0-100, default is 4, 0-20
    xsetwacom set "Wacom Intuos3 6x8 cursor" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    xsetwacom set "Wacom Intuos3 6x8 cursor" CursorProx "10"  # proximity in/out value, default is 10, range?
    xsetwacom set "Wacom Intuos3 6x8 cursor" Mode "Relative"  # or Absolute
    xsetwacom set "Wacom Intuos3 6x8 cursor" Button1 "1"  # left mouse click
    xsetwacom set "Wacom Intuos3 6x8 cursor" Button2 "2"  # middle mouse click
    xsetwacom set "Wacom Intuos3 6x8 cursor" Button3 "3"  # right mouse click

    ## pad = ID 12 = "Wacom Intuos3 6x8 pad"
    xsetwacom set "Wacom Intuos3 6x8 pad" Button1 "key r"  # rectangular selections
    xsetwacom set "Wacom Intuos3 6x8 pad" Button2 "key ctrl shift a"  # select none
    xsetwacom set "Wacom Intuos3 6x8 pad" Button3 "key p"  # paintbrush
    xsetwacom set "Wacom Intuos3 6x8 pad" Button4 "key Tab"  # toggle docks
    xsetwacom set "Wacom Intuos3 6x8 pad" StripLDn "key -"  # zoom out
    xsetwacom set "Wacom Intuos3 6x8 pad" StripLUp "key +"  # zoom in

    xsetwacom set "Wacom Intuos3 6x8 pad" StripRDn "key ALT down"  # brush radius (must be mapped in GIMP)
    xsetwacom set "Wacom Intuos3 6x8 pad" StripRUp "key ALT up"
    xsetwacom set "Wacom Intuos3 6x8 pad" Button5 "key SHIFT CONTROL left"  # prev/next brush (must be mapped in GIMP)
    xsetwacom set "Wacom Intuos3 6x8 pad" Button6 "key SHIFT CONTROL right"
    xsetwacom set "Wacom Intuos3 6x8 pad" Button7 "key CTRL SHIFT e"  # zoom to fit window
    xsetwacom set "Wacom Intuos3 6x8 pad" Button8 "key F11"  # fullscreen

    ## alternate pad button assignment examples for Inkscape courtesy of Robert Saunders
    #Button1 "key F2"  # Node Tool
    #Button2 "key h"  # Undo
    #Button3 "key F1"  # Selection tool
    #Button4 "key Shift"
    #StripLDn "key Prior"  # page up
    #StripLUp "key Next"  # page down

    #StripRDn "key KP_Add"  # NumpadPlus
    #StripRUp "key KP_Subtract"  # NumpadMinus
    #Button5 "key F6"  # Pencil Tool
    #Button6 "key F8"  # Fill and Stroke Dialog
    #Button7 "key F9"  # Toggle View Mode
    #Button8 "key F12"  # Layers Dialog

    ## Developed with duffman.c.d. & artik1024 (& SergeantOreo's Xorg.0.log for coordinates) @ Ubuntu forums

### Intuos4

    ## Device names and ID numbers from 'xinput list' entered in a terminal.
    #
    ## In the example "Device name" not ID # is used.  Note if you use the
    ## xorg.conf the "Device names" will be stylus, eraser, touch, and pad.
    #
    ## If you are hot plugging use "Device name" as ID # can change.
    #
    ## ClickForce changes name to Threshold with xf86-input-wacom 0.10.9 (11-19-10)
    #
    ## Warning:  Changing Mode to either Absolute or Relative in stylus/eraser stops
    ## the mouse from being able to pull guidelines out of the ruler in Gimp.

    ## stylus = ID 12 = "Wacom Intuos4 6x9 stylus"
    xsetwacom set "Wacom Intuos4 6x9 stylus" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom Intuos4 6x9 stylus" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Intuos4 6x9 stylus" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos4 6x9 stylus" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos4 6x9 stylus" PressCurve "5 10 90 95"  # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom Intuos4 6x9 stylus" TPCButton "on"  # stylus tip + button, or "off" for hover mode
    xsetwacom set "Wacom Intuos4 6x9 stylus" Mode "Absolute"  # or Relative cursor movement
    xsetwacom set "Wacom Intuos4 6x9 stylus" Button1 "1"  # left mouse click
    xsetwacom set "Wacom Intuos4 6x9 stylus" Button2 "3"  # right mouse click
    xsetwacom set "Wacom Intuos4 6x9 stylus" Button3 "2"  # middle mouse click
    # default coordinates in /var/log/Xorg.0.log
    #xsetwacom set "Wacom Intuos4 6x9 stylus" Topx "0"
    #xsetwacom set "Wacom Intuos4 6x9 stylus" Topy "0"
    #xsetwacom set "Wacom Intuos4 6x9 stylus" Bottomx "65024"
    #xsetwacom set "Wacom Intuos4 6x9 stylus" Bottomy "40640"

    ## eraser = ID 9 = "Wacom Intuos4 6x9 eraser"
    xsetwacom set "Wacom Intuos4 6x9 eraser" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "Wacom Intuos4 6x9 eraser" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Intuos4 6x9 eraser" ClickForce "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos4 6x9 eraser" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "Wacom Intuos4 6x9 eraser" PressCurve "0 10 90 100"  # Bezier curve, default is 0,0,100,100
    xsetwacom set "Wacom Intuos4 6x9 eraser" Mode "Absolute"  # or Relative cursor movement
    xsetwacom set "Wacom Intuos4 6x9 eraser" Button1 "1"
    # coordinates are the same as used for the stylus
    #xsetwacom set "Wacom Intuos4 6x9 eraser" Topx "0"
    #xsetwacom set "Wacom Intuos4 6x9 eraser" Topy "0"
    #xsetwacom set "Wacom Intuos4 6x9 eraser" Bottomx "65024"
    #xsetwacom set "Wacom Intuos4 6x9 eraser" Bottomy "40640"

    ## cursor = ID 10 = "Wacom Intuos4 6x9 cursor"
    #xsetwacom set "Wacom Intuos4 6x9 cursor" Suppress "4"  # data pt.s trimmed, 0-100, default is 4, 0-20
    #xsetwacom set "Wacom Intuos4 6x9 cursor" RawSample "2"  # data pt.s filtered, default is 2, 0-100
    #xsetwacom set "Wacom Intuos4 6x9 cursor" CursorProx "10"  # proximity in/out value, default is 10, range?
    #xsetwacom set "Wacom Intuos4 6x9 cursor" Mode "Relative"  # or Absolute
    #xsetwacom set "Wacom Intuos4 6x9 cursor" Button1 "1"  # left mouse click
    #xsetwacom set "Wacom Intuos4 6x9 cursor" Button2 "2"  # middle mouse click
    #xsetwacom set "Wacom Intuos4 6x9 cursor" Button3 "3"  # right mouse click

    ## pad = ID 11 = "Wacom Intuos4 6x9 pad"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button2 "key ctrl"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button3 "key alt"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button4 "key shift"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button5 "key tab"

    xsetwacom set "Wacom Intuos4 6x9 pad" AbsWDn "+ "
    xsetwacom set "Wacom Intuos4 6x9 pad" Button1 "core key A "  # button inside touchring
    xsetwacom set "Wacom Intuos4 6x9 pad" AbsWUp "- "

    xsetwacom set "Wacom Intuos4 6x9 pad" Button6 "key apostrophe"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button7 "key backspace"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button8 "key backslash"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button9 "key ctrl z"

    ## alternate pad button assignment examples
    #Button2 "key shift"
    #Button3 "key ctrl"
    #Button4 "key alt"
    #Button5 "key ctrl s"  # save
    #Button5 "key ctrl alt shift O"  # Gimp bush down

    #AbsWDn "+ "
    #Button1 "key ctrl shift f12"  # button inside touchring
    #Button1 "key 1"  # button inside touchring, zoom 100%
    #AbsWUp "- "

    #Button6 "key ctrl b"
    #Button6 "key ctrl alt shift P"  # Gimp bush up
    #Button7 "key ctrl l"
    #Button8 "key ctrl y"
    #Button9 "key ctrl z"

    ## Developed with sebastian0000000 & sanette @ Ubuntu forums
