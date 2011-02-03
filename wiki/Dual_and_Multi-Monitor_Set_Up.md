---
title: Dual and Multi-Monitor Set Up
permalink: wiki/Dual_and_Multi-Monitor_Set_Up/
layout: wiki
tags:
 - HOWTO
---

**Requirements**

-   X Server 1.8 or later
-   xf86-input-wacom-0.10.9 or later

MapToOutput
-----------

The *MapToOutput* parameter maps the wacom tablet to a specific screen.
This only applies for [XRandR](http://www.x.org/wiki/Projects/XRandR)
configurations, not to NVIDIA TwinView. If you don't use the NVIDIA
binary driver, you have a XRandR configuration and can go ahead. If you
use the NVIDIA binary driver, you'll need to manually set the coordinate
mapping.

*MapToOutput* takes an output name as parameter and then adjusts the
coordinate transformation matrix to the screen size of that output. The
monitor names can be obtained with the *xrandr* tool.

    $ xrandr
    Screen 0: minimum 320 x 200, current 3360 x 1200, maximum 8192 x 8192
    VGA1 connected 1920x1200+1440+0 (normal left inverted right x axis y axis) 519mm x 324mm
       1920x1200      60.0*+
       1600x1200      60.0  
       1280x1024      75.0     60.0  
       1152x864       75.0  
       1024x768       75.1     60.0  
       800x600        75.0     60.3  
       640x480        75.0     60.0  
       720x400        70.1  
    LVDS1 connected 1440x900+0+0 (normal left inverted right x axis y axis) 304mm x 190mm
       1440x900       60.1*+   50.1  
       1024x768       60.0  
       800x600        60.3     56.2  
       640x480        59.9  
    DVI1 disconnected (normal left inverted right x axis y axis)

In this example, the three available outputs are "VGA1", "LVDS1" and
"DVI1". To map a tool to the VGA monitor, simply run

` xsetwacom set "Wacom Intuos4 6x9 stylus" MapToOutput VGA1`

[xsetwacom](xsetwacom "wikilink") then goes off, gets the screen size
for the given output (in relation to the total desktop) and calculates a
3x3 matrix that maps the tablet input range to the given output. This
matrix is set as the server's "Device Transformation Matrix". You can
check the matrix by running xinput and looking at the value of this
property.

` xinput list-props "Wacom Intuos4 6x9 stylus"`

Coordinate Transformation Matrix
--------------------------------

If you use the NVIDIA binary driver or are running an earlier version of
xf86-input-wacom, you can manually calculate the matrix and set the
property with the *xinput* tool. While the following may seem a little
daunting if you look at the examples you'll find it is not that hard.

Content Pending.
