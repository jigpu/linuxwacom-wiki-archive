---
title: Xsetwacom
permalink: wiki/Xsetwacom/
layout: wiki
tags:
 - HOWTO
---

<noinclude> xsetwacom is a commandline utility to change wacom driver
settings at runtime. This includes direct settings of driver properties
but also several 'indirect' settings (i.e. the xsetwacom command does
not relate directly to a single property). For a detailed description of
xsetwacom's commands and options, please refer to the man page. This
page provides a general overview and describes some of the
[quirks](#xsetwacom_Command_Parameter_Usage "wikilink") you may
encounter.

Note that any setting changed by xsetwacom will be reset to default (or
a [statically configured setting](/wiki/Configuring_X "wikilink")) whenever
the device is unplugged, disabled or the X Server is restarted.

**A note to developers: do not rely on xsetwacom, as it's UI will change
without warning.**

Usage
=====

</noinclude> To list the applicable devices.

` xsetwacom list`

Which produces output similar to this:

    $> xsetwacom list
    Wacom Intuos4 6x9 eraser            id: 12  type: ERASER    
    Wacom Intuos4 6x9 cursor            id: 13  type: CURSOR    
    Wacom Intuos4 6x9 pad               id: 14  type: PAD       
    Wacom Intuos4 6x9 stylus            id: 15  type: STYLUS    

On the left is the device name, used as parameter in all other xsetwacom
commands. The device ID and device type is provided as well.

The usual invocation to get a parameter is:

` xsetwacom get "Wacom Intuos4 6x9 stylus" Button1`

Replace the device name with the one applicable in your case. This will
print the current value of the parameter, in this case the current
mapping for button 1.

` xsetwacom set "Wacom Intuos4 6x9 stylus" Button1 3`

sets the button 1 to logical button 3 - a right click. Note that
parameters are per-tool and you will need to set the parameter for each
tool if you want them to apply tablet-wide.

Finally, to get a list of parameters just type

` xsetwacom list param`

<noinclude>

xsetwacom Command Parameter Usage
=================================

Remember entering "man xsetwacom" (without the quotes) in a console will
bring up the xsetwacom manual and give you a quick overview of
xsetwacom.

MapToOutput
-----------

The wacom driver does not support multi-monitor setups directly -
handling of such setups is catered for by the X Server.
[xsetwacom](xsetwacom "wikilink") does however provide a parameter to
easily map a tablet to a screen.

**Requirements**

-   X Server 1.8 or later
-   xf86-input-wacom-0.10.9 or later

The *MapToOutput* parameter maps the wacom tablet to a specific screen.
This only applies for [XRandR](http://www.x.org/wiki/Projects/XRandR)
configurations, not to NVIDIA TwinView. If you don't use the NVIDIA
binary driver, you have a XRandR configuration and can go ahead.
*MapToOutput* takes an output name as parameter and then adjusts the
coordinate transformation matrix to the screen size of that output. The
monitor names can be obtained with the *xrandr* tool (e.g. *VGA1*). To
map a tool to the VGA monitor, simply run:

` xsetwacom set "device name" MapToOutput VGA1`

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
*Absolute*, the default for the stylus, eraser, and tablet pc touch.
*Relative Mode* means pointer tracking for the device will function like
a mouse, whereas *Absolute Mode* means the pointer corresponds to the
device's actual position on the tablet or tablet pc screen. An example
of the command would be:

` xsetwacom set "device name" Mode "Absolute"`

Note that using the *Mode* parameter in an xsetwacom command will
prevent you from pulling a *Guideline* in Gimp with your mouse due to a
long standing GTK bug.

PressCurve
----------

The *PressCurve* parameter used by xsetwacom is a [Bezier
curve](http://en.wikipedia.org/wiki/B%C3%A9zier_curve) of third order or
a Bezier Cubic Spline. So it is a cubic equation with four points,
composed of two endpoints or anchor points (0,0 & 100,100) and two
control points (x1,y1 & x2,y2) that define the curve's shape. What that
all means is [xf86-input-wacom](xf86-input-wacom "wikilink")'s X driver
offers you superb control over your stylus and eraser's feel.

With the default curve the two control points have the same values as
the anchor points,

` xsetwacom set "device name" PressCurve 0 0 100 100`

which means it is linear, i.e. reflecting the tablet's designed pressure
response (e.g. 0-1023). Think of it as a 100x100 grid with the Bezier
curve a straight line from the lower left corner (0,0) to the upper
right corner (100,100). To get a "softer" feel (a raised curve) to your
stylus or eraser, you can change the values of the control points to
say:

` xsetwacom set "device name" PressCurve 0 5 95 100`

Note as long as the inner pair of values (y1 & x2) and the outer pair of
values (x1 & y2) each equal 100 you've defined a valid Bezier curve for
xf86-input-wacom. For a "firmer" touch (depressed curve), you could use:

` xsetwacom set "device name" PressCurve 5 0 100 95`

Visually the *PressCurve* parameter covers the range:

      0 100   0 100  # ridiculously soft
      0  50  50 100  # very soft
      0   0 100 100  # linear, the default
     50   0 100  50  # very firm
    100   0 100   0  # unbelievably firm

You can also combine changes to the inner and outer pairs simultaneously
(altering the control points relative to each other) to achieve the feel
you prefer with your stylus and eraser.

A graphical javascript demo is available
[here](http://linuxwacom.sourceforge.net/misc/bezier.html). It is more
generalized than the implementation in xf86-input-wacom. But it is easy
enough to only drag the two control points and follow the constraints
described above to duplicate the effects of your *PressCurve* parameter
settings.

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

Note that rotation is tool-specific. If you rotate the stylus tool, the
eraser tool and all others still have the original rotation. To rotate
the complete tablet, rotate all tools. To rotate a tool use:

` xsetwacom set "device name" Rotate half`

and one of the four valid settings. For more detail see
[Rotation](/wiki/Rotation "wikilink").

TPCButton
---------

The Tablet PC Button i.e. *TPCButton* parameter is "on" by default for
tablet pc's. In order for the stylus buttons to work the stylus tip must
be in contact with the screen. The default for tablets is "off" which
enables the *hover* mode. In tablet or hover mode the stylus buttons
will work with the stylus tip in proximity of the tablet, but the stylus
tip does not actually have to be touching. So the values are on\|off
(tablet pc mode\|tablet/hover mode) and apply only to the stylus.

` xsetwacom set "device name" TPCButton "on"`

Note that *TPCButton* parameter functionality was added to xsetwacom in
the *xf86-input-wacom-0.10.10+ tree*. If you want to use this xsetwacom
parameter, either clone the
[xf86-input-wacom](xf86-input-wacom "wikilink") git repository, or
download a later tar.

Table of New Parameter Names
============================

**Information Pending** - this entry posted in anticipation of upcoming
changes, does not apply yet.

Most of the new parameter names apply starting with
[xf86-input-wacom-0.10.11](http://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/)
released 2-17-11(?). Some were changed or removed earlier. Many of the
parameter names were changed to make them more readable. Some were
changed or removed for code stream-lining purposes, such as adding the
whitespace between Button and the number, i.e. Button5 becoming Button
5.

| New Parameter Name                 | Old Parameter Name                 |
|------------------------------------|------------------------------------|
| AbsWheelUp                         | AbsWUp                             |
| AbsWheelDown                       | AbsWDn                             |
| Area x1 y1 x2 y2                   | TopX, TopY, BottomX, & BottomY     |
| Button 1 ...                       | Button1 ...                        |
| CursorProximity                    | CursorProx                         |
| removed                            | GetTabletID                        |
| MapToOutput                        | new                                |
| removed                            | mmonitor                           |
| PressureCurve                      | PressCurve                         |
| RelWheelUp                         | RelWUp                             |
| RelWheelDown                       | RelWDn                             |
| ResetArea                          | xyDefault                          |
| Rotate values: none, cw, ccw, half | Rotate values: NONE, CW, CCW, HALF |
| removed                            | Rspeed                             |
| removed                            | Screen\_No                         |
| StripLeftUp                        | StripLUp                           |
| StripLeftDown                      | StripLDn                           |
| StripRightUp                       | StripRUp                           |
| StripRightDown                     | StripRDn                           |
| TabletPCButton                     | TPCButton                          |
| Threshold                          | ClickForce                         |
| removed                            | TVResolution                       |
| removed                            | TwinView                           |
|                                    |                                    |

</noinclude>
