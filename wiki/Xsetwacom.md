---
title: Xsetwacom
permalink: wiki/Xsetwacom/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

<noinclude> xsetwacom is a commandline utility to change wacom driver
settings at runtime. This includes direct settings of driver properties
but also several 'indirect' settings (i.e. the xsetwacom command does
not relate directly to a single property). For a detailed description of
xsetwacom's commands and options, please refer to the man page. Also see
[Tablet Configuration](/wiki/Tablet_Configuration "wikilink") for examples of
how to use xsetwacom. This page provides a general overview and
describes some of the
[quirks](#xsetwacom_Command_Parameter_Usage "wikilink") you may
encounter.

**Any setting changed by xsetwacom will be reset to default** (or a
[statically configured setting](/wiki/Configuring_X "wikilink")) whenever the
device is unplugged, disabled or the X Server is restarted.

Usage
=====

To list the applicable devices.

` xsetwacom list`

Which produces output similar to this:

    $> xsetwacom list
    Wacom Intuos4 6x9 eraser            id: 12  type: ERASER    
    Wacom Intuos4 6x9 cursor            id: 13  type: CURSOR    
    Wacom Intuos4 6x9 pad               id: 14  type: PAD       
    Wacom Intuos4 6x9 stylus            id: 15  type: STYLUS    

On the left is the device name, used as parameter in all other xsetwacom
commands. The device ID and device type is provided as well.

</noinclude> The usual invocation to get a parameter is:

` xsetwacom get "Wacom Intuos4 6x9 stylus" Button 1`

Replace the device name with the one applicable in your case. This will
print the current value of the parameter, in this case the current
mapping for button 1.

` xsetwacom set "Wacom Intuos4 6x9 stylus" Button 1 3`

sets the button 1 to logical button 3 - a right click. Note that
parameters are per-tool and you will need to set the parameter for each
tool if you want them to apply tablet-wide. <noinclude> </noinclude>

Finally, to get a list of parameters just type

` xsetwacom list parameters`

<noinclude>

xsetwacom Command Parameter Usage
=================================

Remember entering "man xsetwacom" (without the quotes) in a console will
bring up the xsetwacom manual and give you a quick overview of
xsetwacom.

MapToOutput
-----------

See [MapToOutput](/wiki/Dual_and_Multi-Monitor_Set_Up#MapToOutput "wikilink")
for more detail. If you use the NVIDIA binary driver or are running an
earlier version of xf86-input-wacom, you can manually calculate the
matrix and set the property with the *xinput* tool. See [Coordinate
Transformation
Matrix](/wiki/Dual_and_Multi-Monitor_Set_Up#Coordinate_Transformation_Matrix "wikilink").

Mode
----

The *Mode* parameter sets the device mode as either *Relative*, the
default for cursor (the Wacom tablet mouse) and tablet touch or
*Absolute*, the default for the stylus, eraser, and tablet PC touch.
*Relative Mode* means pointer tracking for the device will function like
a mouse, whereas *Absolute Mode* means the pointer corresponds to the
device's actual position on the tablet or tablet PC screen. An example
of the command would be:

` xsetwacom set "device name" Mode "Absolute"`

Note that using the *Mode* parameter in an xsetwacom command will
prevent you from pulling a *Guideline* in Gimp with your mouse due to a
long standing GTK bug.

PressureCurve
-------------

The *PressureCurve* parameter used by xsetwacom is a [Bezier
curve](http://en.wikipedia.org/wiki/B%C3%A9zier_curve) of third order or
a Bezier Cubic Spline. So it is a cubic equation with four points,
composed of two endpoints or anchor points (0,0 & 100,100) and two
control points (x1,y1 & x2,y2) that define the curve's shape. What that
all means is [xf86-input-wacom](xf86-input-wacom "wikilink")'s X driver
offers you superb control over your stylus and eraser's feel.

With the default curve the two control points have the same values as
the anchor points,

` xsetwacom set "device name" PressureCurve 0 0 100 100`

which means it is linear, i.e. reflecting the tablet's designed pressure
response (e.g. 0-1023). Think of it as a 100x100 grid with the Bezier
curve a straight line from the lower left corner (0,0) to the upper
right corner (100,100). To get a different feel, you can change the
values of the control points. Example values are:

      0 100   0 100  # ridiculously soft
      0  50  50 100  # very soft
      0   0 100 100  # linear, the default
     50   0 100  50  # very firm
    100   0 100   0  # unbelievably firm

You can also combine changes to the inner and outer pairs simultaneously
(altering the control points relative to each other) to achieve the feel
you prefer with your stylus and eraser.

A graphical javascript demo is available
[here](http://linuxwacom.sourceforge.net/misc/bezier.html). Drag the two
control points and follow the constraints described above to duplicate
the effects of your *PressureCurve* parameter settings. Note that a
valid pressure curve setting in
[xf86-input-wacom](xf86-input-wacom "wikilink") must have all four
values between 0 and 100.

Rotate
------

The wacom driver supports rotation by 90 degree angles. The *Rotate*
parameter can be applied at runtime with xsetwacom or statically
(through the [configuration files](/wiki/Configuring_X "wikilink")). The four
valid settings are:

-   **none**: the tablet is not rotated in software and uses its natural
    rotation.
-   **half**: the tablet is rotated by 180 degrees (upside-down)
-   **cw**: the tablet is rotated 90 degrees clockwise
-   **ccw**: the tablet is rotated 90 degrees counter-clockwise

Note that rotation is a tablet-wide option. If you rotate one input tool
all other tools associated with the same tablet are rotated to the same
orientation. To rotate a tool use:

` xsetwacom set "device name" Rotate half`

and one of the four valid settings. For more detail see
[Rotation](/wiki/Rotation "wikilink").

TabletPCButton
--------------

The Tablet PC Button behaviour.

-   If TabletPCButton is **on**, the stylus must be in contact with the
    screen for any stylus button to work.
-   if TabletPCButton is **off**, stylus buttons will work once the
    stylus is in proximity of the tablet (regardless of whether it is
    touching the screen).

*TabletPCButton* is *on* by default for Tablet PCs and *off* for all
other models. An example xsetwacom command to set the behaviour is:

` xsetwacom set "device name" TabletPCButton "on"`

Note that the *TabletPCButton* parameter is only available in
*xf86-input-wacom-0.10.10* or later.

Interaction with gnome-settings-daemon
--------------------------------------

If you run, for example, the following command:

`xsetwacom -s get "Wacom Intuos Pro M Pad pad" all`

The output may include something like the following:

<code>

    xsetwacom set "Wacom Intuos Pro M Pad pad" "RelWheelUp" "1" "button +90 -90 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "RelWheelDown" "2" "button +91 -91 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "AbsWheelUp" "3" "button +90 -90 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "AbsWheelDown" "4" "button +91 -91 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "AbsWheel2Up" "5" "button +92 -92 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "AbsWheel2Down" "6" "button +93 -93 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "StripLeftUp" "1" "button +94 -94 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "StripLeftDown" "2" "button +95 -95 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "StripRightUp" "3" "button +96 -96 "
    xsetwacom set "Wacom Intuos Pro M Pad pad" "StripRightDown" "4" "button +97 -97 "

</code>

The 90/91/etc indicates that gnome-settings-daemon (or some fork of it)
is trying to control the tablet. g-s-d listens for these buttons
(90/91/etc) to determine if you've used those particular parts of the
pad.

Table of New Parameter Names
============================

[xsetwacom](xsetwacom "wikilink")'s parameter names have changed over
several revisions (the biggest change happened in
[xf86-input-wacom-0.10.11](http://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/),
released Feb 16, 2011). Reasons for changing parameters in xsetwacom
include UI improvements or improved maintainability of the code.

| Old Parameter Name                 | New Parameter Name                 |
|------------------------------------|------------------------------------|
| AbsWUp                             | AbsWheelUp                         |
| AbsWDn                             | AbsWheelDown                       |
| TopX, TopY, BottomX, & BottomY     | Area x1 y1 x2 y2                   |
| Button1 ...                        | Button 1 ...                       |
| CursorProx                         | CursorProximity                    |
| GetTabletID                        | *removed*                          |
| *no equivalent*                    | MapToOutput                        |
| mmonitor                           | *removed*                          |
| PressCurve                         | PressureCurve                      |
| RelWUp                             | RelWheelUp                         |
| RelWDn                             | RelWheelDown                       |
| xyDefault                          | ResetArea                          |
| Rotate values: NONE, CW, CCW, HALF | Rotate values: none, cw, ccw, half |
| Rspeed                             | *removed*                          |
| Screen\_No                         | *removed*                          |
| StripLUp                           | StripLeftUp                        |
| StripLDn                           | StripLeftDown                      |
| StripRUp                           | StripRightUp                       |
| StripRDn                           | StripRightDown                     |
| CommonDBG                          | TabletDebugLevel                   |
| TPCButton                          | TabletPCButton                     |
| ClickForce                         | Threshold                          |
| DebugLevel                         | ToolDebugLevel                     |
| TVResolution                       | *removed*                          |
| TwinView                           | *removed*                          |
|                                    |                                    |

</noinclude>
