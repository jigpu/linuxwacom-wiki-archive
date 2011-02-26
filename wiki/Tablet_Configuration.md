---
title: Tablet Configuration
permalink: wiki/Tablet_Configuration/
layout: wiki
tags:
 - HOWTO
---

**In Progress**

This page shows you how to assemble a runtime or static configuration
for a specific tablet or tablet PC model. Additionally it demonstrates
how to create profiles for various applications.

The linuxwacom project welcomes additions to its mediawiki so please
join in if you have contributions.

Currently [xf86-input-wacom](xf86-input-wacom "wikilink") does not have
a configuration gui like linuxwacom's Wacom Control Panel (wacomcpl).
However since **wacomcpl** stored its settings as a
[xsetwacom](xsetwacom "wikilink") script in **.xinitrc** you can
essentially duplicate the .xinitrc, calling it say **.xsetwacom.sh**.
**This can be considered a work around** while waiting for the
development of a Tablet Configuration GUI, see [Graphical Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

It should be relatively straight forward to assemble a configuration
script for your tablet. Be sure to read *man wacom*, *man xsetwacom*,
[Xsetwacom](/wiki/Xsetwacom "wikilink"), and enter *xsetwacom list parameters*
in a terminal to learn about the parameters or Options you may want to
change. As you will see a script can give you some finer control over
your tablet's features and also let you create profiles.

**Note:** Be aware that the xsetwacom interface may change without
notice. If this happens feel free to update and correct this linux wacom
project mediawiki page.

If a xsetwacom command stops working your friend is the get command.

Runtime (xsetwacom) Configuration
---------------------------------

### Stylus, Eraser, Cursor, Touch

The various sections below demonstrate most of the valid *xsetwacom set*
command parameters that apply to a given input tool. The values shown
are the defaults. Find the relevant "device name" by entering
*[xinput](xinput "wikilink") list* in a console.

*Stylus* section

    xsetwacom set "device name" Suppress 4
    xsetwacom set "device name" RawSample 2
    xsetwacom set "device name" Threshold 27
    xsetwacom set "device name" PressureCurve 0 0 100 100
    xsetwacom set "device name" TabletPCButton on  # default for tablet PCs, it's off for tablets
    xsetwacom set "device name" Mode Absolute
    xsetwacom set "device name" Button 1 1
    xsetwacom set "device name" Button 2 3
    xsetwacom set "device name" Button 3 2
    xsetwacom set "device name" Area 0 0 ?? ??

*Eraser* section

    xsetwacom set "device name" Suppress 4
    xsetwacom set "device name" RawSample 2
    xsetwacom set "device name" Threshold 27
    xsetwacom set "device name" PressureCurve 0 0 100 100
    xsetwacom set "device name" Mode Absolute
    xsetwacom set "device name" Button 1 1
    xsetwacom set "device name" Area 0 0 ?? ??

*Cursor* section

    xsetwacom set "device name" Suppress 4 
    xsetwacom set "device name" RawSample 2
    xsetwacom set "device name" CursorProximity 10  # default depends on tablet
    xsetwacom set "device name" Mode Relative
    xsetwacom set "device name" Button 1 1
    xsetwacom set "device name" Button 2 2
    xsetwacom set "device name" Button 3 3
    xsetwacom set "device name" Area 0 0 ?? ??

*Touch, single finger* (1FGT) section

    xsetwacom set "device name" Touch on
    xsetwacom set "device name" Suppress 4
    xsetwacom set "device name" RawSample 2
    xsetwacom set "device name" Threshold 27
    xsetwacom set "device name" Mode Absolute
    xsetwacom set "device name" TapTime 250
    xsetwacom set "device name" Button 1 1
    xsetwacom set "device name" Area 0 0 ?? ??

*Touch, two finger* (2FGT) section

    xsetwacom set "device name" Touch on
    xsetwacom set "device name" Gesture on
    xsetwacom set "device name" Suppress 4
    xsetwacom set "device name" RawSample 2
    xsetwacom set "device name" Threshold 27
    xsetwacom set "device name" Mode Relative
    xsetwacom set "device name" Button 1 1
    xsetwacom set "device name" ZoomDistance 50
    xsetwacom set "device name" ScrollDistance 20
    xsetwacom set "device name" TapTime 250
    xsetwacom set "device name" Area 0 0 ?? ??

### Pad

The sections below demonstrate a sample of the various tablets with
tablet buttons or a *pad*. They are ordered to somewhat mimic the
physical layout of the various pads. Enter *xsetwacom list modifiers* in
a console to see the modifiers and special keys available. You can also
use *dumpkeys* to check what keys are available on your system. You need
to be root to run it. The commands with probably the two most useful
switches are *sudo dumpkeys --long-info* and *sudo dumpkeys
--funcs-only*, if you have sudo installed. See *man dumpkeys*.

#### Bamboo Pen & Touch

The following button values or assignments are the defaults.

    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 1 1
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 2 2
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 3 3
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 4 4

**Since the defaults are not very illuminating the following contains a
hodgepodge of possible Button assignments to illustrate the
possibilities. These assignments are just examples.**

#### Bamboo

    xsetwacom set "Wacom BambooFun 4x5 pad" Button 1 "key esc"
    xsetwacom set "Wacom BambooFun 4x5 pad" Button 2 "key F12 "
    xsetwacom set "Wacom BambooFun 4x5 pad" AbsWDn "key ctrl shift s"
    xsetwacom set "Wacom BambooFun 4x5 pad" AbsWUp "key ctrl shift s"
    xsetwacom set "Wacom BambooFun 4x5 pad" Button 3 "key KP_Next"
    xsetwacom set "Wacom BambooFun 4x5 pad" Button 4 "key KP_Prior"

#### Intuos3

Gimp pad button examples

    xsetwacom set "Wacom Intuos3 6x8 pad" Button 1 "key r"  # rectangular selections
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 2 "key ctrl shift a"  # select none
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 3 "key p"  # paintbrush
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 4 "key tab"  # toggle docks
    xsetwacom set "Wacom Intuos3 6x8 pad" StripLDn "key minus"  # zoom out
    xsetwacom set "Wacom Intuos3 6x8 pad" StripLUp "key shift plus"  # zoom in

    xsetwacom set "Wacom Intuos3 6x8 pad" StripRDn "key alt down"  # brush radius (must be mapped in GIMP)
    xsetwacom set "Wacom Intuos3 6x8 pad" StripRUp "key alt up"
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 5 "key shift control left"  # prev/next brush (must be mapped in GIMP; disables float)
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 6 "key shift control right"
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 7 "key ctrl shift e"  # fit Image in window
    xsetwacom set "Wacom Intuos3 6x8 pad" Button 8 "key F11"  # fullscreen

Inkscape pad button examples

    Button 1 "key F2"  # Node Tool
    Button 2 "key h"  # Undo
    Button 3 "key F1"  # Selection tool
    Button 4 "key shift"
    StripLDn "key Prior"  # page up
    StripLUp "key Next"  # page down

    StripRDn "key KP_Add"  # NumpadPlus
    StripRUp "key KP_Subtract"  # NumpadMinus
    Button 5 "key F6"  # Pencil Tool
    Button 6 "key F8"  # Fill and Stroke Dialog
    Button 7 "key F9"  # Toggle View Mode
    Button 8 "key F12"  # Layers Dialog

#### Intuos4

    xsetwacom set "Wacom Intuos4 6x9 pad" Button 2 "key ctrl"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button 3 "key alt"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button 4 "key shift"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button 5 "key ctrl alt shift o"  # Gimp brush down

    xsetwacom set "Wacom Intuos4 6x9 pad" AbsWDn "key shift plus"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button1 "key 1"  # button inside touchring, zoom 100%
    xsetwacom set "Wacom Intuos4 6x9 pad" AbsWUp "key minus"

    xsetwacom set "Wacom Intuos4 6x9 pad" Button 6 "key ctrl alt shift p"  # Gimp brush up
    xsetwacom set "Wacom Intuos4 6x9 pad" Button 7 "key backspace"
    xsetwacom set "Wacom Intuos4 6x9 pad" Button 8 "key ctrl s"  # save
    xsetwacom set "Wacom Intuos4 6x9 pad" Button 9 "key ctrl z"  # Undo in Gimp

#### Cintiq 21

    xsetwacom set "Wacom Cintiq 21UX2 pad" StripLDn "key minus"  # zoom out
    xsetwacom set "Wacom Cintiq 21UX2 pad" StripLUp "key shift plus"  # zoom in
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 1 "key r"  # rectangular selections
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 2 "key ctrl shift a"  # select none
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 3 "key p"  # paintbrush
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 4 "key tab"  # toggle docks
    ## circle "button", need to figure out.
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 5 "key shift control left"  # prev/next brush (must be mapped in GIMP)
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 6 "key shift control right" 
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 7 "key ctrl shift e"  #  fit Image in window
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 8 "key F11"  # fullscreen

    xsetwacom set "Wacom Cintiq 21UX2 pad" StripRDn "key alt down"  # brush radius (must be mapped in GIMP)
    xsetwacom set "Wacom Cintiq 21UX2 pad" StripRUp "key alt up"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 9 "key ctrl"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 10 "key alt"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 11 "key shift"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 12 "key tab"
    ## circle "button", need to figure out.
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 13 "key apostrophe"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 14 "key backspace"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 15 "key backslash"
    xsetwacom set "Wacom Cintiq 21UX2 pad" Button 16 "key ctrl z"  # Undo in Gimp

### Sample Runtime Script

Now say I have a Bamboo Pen and Touch tablet and I want to write a
runtime script for it. I want to change the PressureCurve for the stylus
and eraser, using a softer one for the stylus and a firmer one for the
eraser. Also I have the GTK CPU utilization bug in Gimp so I want to
adjust RawSample up to decrease the lag between where the stylus tip is
and when the line is drawn in Gimp. On top of all that I noticed the
stylus wasn't quite reaching the top and left screen edges so after
[calibrating](/wiki/Calibration "wikilink") I have some new tablet area values
I want to use. I'm a pretty quick double tapper who drinks a little too
much caffeine and I want to decrease my TapTime a little. 250 msec is an
eternity! Plus I want my scrolling a little more sensitive. And I want
my pad (tablet buttons) to have the default Windows XP setup, just
because.

I need to obtain my "device names" by running the [xinput
list](xinput "wikilink") command in a terminal. Now I can proceed to
assemble the following shell script.

    #!/bin/sh

    ## Stylus
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" RawSample 20
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" PressureCurve 0 10 90 100
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen stylus" Area -10 -20 14720 9200

    ## Eraser
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen eraser" RawSample 20
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen eraser" PressureCurve 5 0 100 95
    xsetwacom set "Wacom BambooFun 2FG 4x5 Pen eraser" Area -10 -20 14720 9200

    ## Touch (2FGT)
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" ScrollDistance 18
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger touch" TapTime 220

    ## Pad
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 1 "key ctrl t"  # key combination for toggle touch script
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 2 "key backspace"
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 3 3  # right mouse click
    xsetwacom set "Wacom BambooFun 2FG 4x5 Finger pad" Button 4 1  # left mouse click

In the sample script above the "device names" were used because they
don't change with hot plugging. The ID \# can be used for convience when
using a get or set command during a session. However it can change after
a hot plug of the tablet or another usb device.

### Setting Up the Script to Run at Startup

Now I want my new shell script configuration to apply even after a
restart. While you can change your settings on the fly using xsetwacom
commands in a terminal, because they are runtime commands they only
apply during the current session. So you need to set a xsetwacom script
to auto-start. Create a file and enter the contents of the script you
want into it. Name it .xsetwacom.sh (or whatever you prefer).

Place the script in a file called bin/.xsetwacom.sh in your home
directory. You may need to create the bin directory in
/home/yourusername/ first. Many distributions recommend placing user
binaries and scripts in a user /bin directory rather than just in
/home/yourusername/. Make the script executable with:

` chmod +x $HOME/bin/.xsetwacom.sh`

The . in front of xsetwacom makes it a hidden file. You don't have to do
that although it helps prevent directory clutter. Then in Gnome go to
System &gt; Preferences &gt; Startup Applications (or your
distribution's equivalent) and click on Add and for the command write
"sh /home/yourusername/bin/.xsetwacom.sh" (without the quotes).

### Profiles

A *Profile* contains the settings you prefer for an individual program
you use your tablet in. Usually it consists of modifying the pad button
assignments but can also involve changing the PressureCurve or Stylus
button assignment or whatever else is useful for a given program. To
select different pad button assignments you should become familiar with
the keyboard shortcuts available in the program you are interested in.
For e.g. with Gimp look in *Edit &gt; Keyboard Shortcuts* or *Edit &gt;
Preferences &gt; Interface &gt; Configure Keyboard Shortcuts*. For
Inkscape it's *Help &gt; Keys and Mouse reference* which takes you to
the keyboard shortcuts online. Some of the pad sections above contained
possible Gimp and Inkscape button assignments.

So let's write a Gimp profile for a Bamboo Pen and Touch tablet. If you
plan on a long or multiple scripts for profiles the following script
technique using variable assignment for your "device names" might make
them a little easier to write. A Bamboo Pen & Touch is probably not the
best example. With most tablets you would use just the DEVICE variable.
But because the Bamboo P&T is seen by the system as 2 devices,
stylus/eraser & touch/pad, it needs two DEVICE variables. Or you could
use the vanilla xsetwacom commands like in the [Sample Runtime
Script](#Sample_Runtime_Script "wikilink").

Say the stylus and eraser are already set the way you prefer in Gimp by
the startup script, so no need to modify them. Especially since the
stylus Button 3 is already set to middle click (2). Which is the hand
(grab) in Gimp. And right click (3) brings up the Menus which is fine.
You want to turn touch off, because you don't want it on in Gimp and it
also frees up a precious button. Then you assign the four pad buttons to
commands you find useful in Gimp.

    #!/bin/bash

    DEVICE="Wacom BambooFun 2FG 4x5 Pen"
    DEVTWO="Wacom BambooFun 2FG 4x5 Finger"

    STYLUS="$DEVICE stylus"
    ERASER="$DEVICE eraser"
    TOUCH="$DEVTWO touch"
    PAD="$DEVTWO pad"

    ## Stylus

    ## Eraser

    ## Touch (2FGT)
    xsetwacom set "$TOUCH" Touch off

    ## Pad
    xsetwacom set "$PAD" Button 1 "key shift ctrl n"  # New layer
    xsetwacom set "$PAD" Button 2 "key minus"  # Zoom out
    xsetwacom set "$PAD" Button 3 "key shift plus"  # Zoom in
    xsetwacom set "$PAD" Button 4 "key control z"  # Undo

Call it Gimp\_Profile.sh or .Gimp\_Profile.sh (or whatever you prefer)
and Save. Remember you have to make the profile script executable. Now
let's write an Inkscape profile. In Inkscape it turns out you prefer the
stylus pressure softer and you want the eraser at the default (linear)
pressure. Middle click is also grab, so that's good. Touch is again off
for the same reasons. You also want a different set of pad commands,
ones that are useful in Inkscape.

    #!/bin/bash

    DEVICE="Wacom BambooFun 2FG 4x5 Pen"
    DEVTWO="Wacom BambooFun 2FG 4x5 Finger"

    STYLUS="$DEVICE stylus"
    ERASER="$DEVICE eraser"
    TOUCH="$DEVTWO touch"
    PAD="$DEVTWO pad"

    ## Stylus
    xsetwacom set "$STYLUS" PressureCurve 0 15 85 100

    ## Eraser
    xsetwacom set "$ERASER" PressureCurve 0 0 100 100

    ## Touch (2FGT)
    xsetwacom set "$TOUCH" Touch off

    ## Pad
    xsetwacom set "$PAD" Button 1 "key F2"  # Node tool
    xsetwacom set "$PAD" Button 2 "key F1"  # Selection tool
    xsetwacom set "$PAD" Button 3 "key F9"  # Toggle view mode
    xsetwacom set "$PAD" Button 4 "key h"  # Undo

Call it Inkscape\_Profile.sh or .Inkscape\_Profile.sh then Save and make
it executable.

#### Launcher for Profile

In Gnome right click on your Desktop and chose *Create Launcher...* In
your distribution do the equivalent. In the Launcher enter a name, such
as Inkscape Profile. Then in the Command box add the entire path for the
script. It's a good idea to select an icon also. Close and the profile
script will now run when you double click on the Launcher.

It's also a good idea to set up your general or start up configuration
script in a Launcher. That way when you're done using the application a
particular profile applied to, you can restore your general
configuration.

Static (xorg.conf.d or xorg.conf) Configuration
-----------------------------------------------

Entering *man wacom* in a terminal will bring up the Options you can
enter into
[/etc/udev/X11/52-wacom-options.conf](/wiki/Configuring_X "wikilink") or in
the xorg.conf.
