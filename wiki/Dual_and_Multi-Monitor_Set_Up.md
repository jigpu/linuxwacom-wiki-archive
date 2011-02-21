---
title: Dual and Multi-Monitor Set Up
permalink: wiki/Dual_and_Multi-Monitor_Set_Up/
layout: wiki
tags:
 - HOWTO
---

<noinclude> **Requirements**

-   X Server 1.8 or later
-   xf86-input-wacom-0.10.9 or later

MapToOutput
-----------

</noinclude>

The wacom driver does not support multi-monitor setups directly -
handling of such setups is handled by the X Server.
[xsetwacom](xsetwacom "wikilink") does however provide a parameter to
easily map a tablet to a screen

The *MapToOutput* parameter maps the wacom tablet to a specific screen.
*MapToOutput* takes an output name as parameter and then adjusts the
coordinate transformation matrix to the screen size of that output. The
monitor names can be obtained with the *xrandr* tool (e.g. *VGA1*).

<noinclude>

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
"DVI1". </noinclude>

To map a tool to the VGA monitor, simply run

` xsetwacom set "Wacom Intuos4 6x9 stylus" MapToOutput VGA1`

<noinclude> [xsetwacom](xsetwacom "wikilink") then goes off, gets the
screen size for the given output (in relation to the total desktop) and
calculates a 3x3 matrix that maps the tablet input range to the given
output. This matrix is set as the server's "Device Transformation
Matrix". You can check the matrix by running xinput and looking at the
value of this property.

` xinput list-props "Wacom Intuos4 6x9 stylus"`

Coordinate Transformation Matrix
--------------------------------

If you use the NVIDIA binary driver or are running an earlier version of
xf86-input-wacom, you can manually calculate the matrix and set the
property with the *xinput* tool. While the following may seem a little
daunting if you look at the examples you'll find it is not that hard.
The code does the matrix multiplication for you. You are determining the
matrix values by solving fractions involving your monitors' dimensions.

First determine the "device name" of what you want bound on a specific
screen. Your choices will be the stylus, eraser, or cursor (Wacom tablet
mouse). In a terminal enter the following.

    xinput list

Using the "device name" (in quotes) enter in a terminal.

    xinput list-props "device name"

For example:

    xinput list-props "Wacom BambooFun 2FG 6x8 Pen stylus"

This will give an output that contains your current 'Coordinate
Transformation Matrix' for the device as the X server sees it.

    Coordinate Transformation Matrix (123):    1.000000, 0.000000, 0.000000, 0.000000, 1.000000, 0.000000, 0.000000, 0.000000, 1.000000

Now let's do a quick overview of the math involved. Notice that while it
is an Identity matrix, $\\bigl( \\begin{smallmatrix}
1 & 0 & 0 \\\\
0 & 1 & 0 \\\\
0 & 0 & 1 \\\\
\\end{smallmatrix} \\bigr)$ which has the effect of multiplying by 1, or
in other words no transformation, the addition of a third vector value
(beyond X & Y) makes it an affine transformation with homogeneous
coordinates. We need to determine the transform matrix appropriate to
the monitor you want to place the device on.

What we will do with the transform is translate the currently assigned
pixel coordinate vector X,Y of your tablet device to a new pixel
coordinate vector X',Y'.

The numbers in the X server "Coordinate Transformation Matrix" are a 3x3
matrix read out row by row. Using an affine transformation means
translation can be expressed with matrix multiplication. The equation we
are looking at is in the form of:

<center>
$\\begin{pmatrix}
x' \\\\
y' \\\\
1  \\\\
\\end{pmatrix}
= 
\\begin{pmatrix}
c0 & c1 & c2 \\\\
c3 & c4 & c5 \\\\
c6 & c7 & c8 \\\\
\\end{pmatrix}

\\begin{pmatrix}
x \\\\
y \\\\
w \\\\
\\end{pmatrix}$

</center>
The third vector value w (the row) 0 0 1, with c8 or w always equal to
1, is what makes it affine.

The associated linear equations would be:

<center>
$\\begin{align}
x' & = & (c0x + c1y + c2) / w'\\\\
y' & = & (c3x + c4y + c5) / w'\\\\
w' & = & (c6x + c7y + c8) = 1\\\\
\\end{align}$

</center>
So c0 and c4 corresponds to the scaling on the X and Y axes, c2 and c5
corresponds to the translation (X & Y offsets) on those axes, and c6,
c7, and c8 are always 0, 0 and 1.

### Dual Monitors

#### Two monitors with the same resolution

\- the simplest case

Say both monitors are 1280x1024. You first set up the linear transform
(matrix) like so.

      left:                               right:
    [ 1280/(1280+1280)  0         0 ]   [ 1280/(1280+1280)  0          1280/(1280+1280)]
    [ 0                 1024/1024 0 ]   [ 0                 1024/1024  0               ]
    [ 0                 0         1 ]   [ 0                 0          1               ]

Using fractions amounts to scaling from 0-1 so you end up with the
following.

      left:         right:
    [ 0.5 0 0 ]   [ 0.5 0 0.5 ]
    [ 0   1 0 ]   [ 0   1 0   ]
    [ 0   0 1 ]   [ 0   0 1   ]

With the c2 = 0.5 in the right matrix being the x offset.

Now that you've determined the transform use the appropriate xinput
command for the monitor you want your device on:

*Left monitor*

    xinput set-prop "device name" --type=float "Coordinate Transformation Matrix" 0.5 0 0 0 1 0 0 0 1

*Right monitor*

    xinput set-prop "device name" --type=float "Coordinate Transformation Matrix" 0.5 0 0.5 0 1 0 0 0 1

#### Two monitors with different resolutions

example courtesy of Rumtscho @ Ubuntu forums

Left monitor 1280x1024 & right monitor 2560x1440. Setting up the linear
transform (matrix).

      left:                                right:
    [ 1280/(1280+2560)  0          0 ]   [ 2560/(1280+2560)  0          1280/(1280+2560)]
    [ 0                 1024/1440  0 ]   [ 0                 1440/1440  0               ]
    [ 0                 0          1 ]   [ 0                 0          1               ]

Since for Rumtscho 'xinput list' returned the stylus "device name" as
"Wacom BambooFun 2FG 6x8 Pen stylus" the appropriate xinput commands for
device placement on the monitors are these.

*Left monitor*

    xinput set-prop "Wacom BambooFun 2FG 6x8 Pen stylus" --type=float "Coordinate Transformation Matrix" 0.333333 0 0 0 .711111 0 0 0 1

*Right monitor*

    xinput set-prop "Wacom BambooFun 2FG 6x8 Pen stylus" --type=float "Coordinate Transformation Matrix" 0.666666 0 0.333333 0 1 0 0 0 1

#### Two monitors with one rotated

example courtesy of lejono @ Ubuntu forums

Left monitor 1600x1200 & right monitor (tablet pc) 1280x800. Tablet pc
in laptop mode.

      left:                                right:
    [ 1600/(1600+1280)  0          0 ]   [ 1280/(1600+1280)  0         1600/(1600+1280)]
    [ 0                 1200/1200  0 ]   [ 0                 800/1200  0               ]
    [ 0                 0          1 ]   [ 0                 0         1               ]

*Left monitor*

    xinput set-prop "Serial Wacom Tablet stylus" --type=float "Coordinate Transformation Matrix" 0.555555 0 0 0 1 0 0 0 1

*Right monitor*

    xinput set-prop "Serial Wacom Tablet stylus" --type=float "Coordinate Transformation Matrix" 0.444444 0 0.555555 0 0.666666 0 0 0 1

Now if we rotate to tablet mode we are adding a rotional transformation.
Let's use rotation by an angle A counter-clockwise about the origin. The
functional form would be x' = xcosA − ysinA and y' = xsinA + ycosA but
we want to make it affine so we can use multiplication. And we need to
add it to the current matrix. So written in matrix form, it becomes the
following.

    [ x' ]   [ cosA*c0 -sinA*c1 c2 ]   [ x ]
    [ y' ] = [ sinA*c3  cosA*c4 c5 ] * [ y ]
    [ 1  ]   [ c6       c7      c8 ]   [ w ]

representing the equations:

    x' = (cosA*c0x + -sinA*c1y + c2) / w'
    y' = (sinA*c3x +  cosA*c4y + c5) / w'
    w' = (c6x      +  c7y      + c8) = 1

We aren't rotating the left monitor so its matrix doesn't change and we
can ignore it. If we rotate the tablet pc 180 degrees to inverted, since
cos(180) = -1 and sin(180) = 0 you end up with:

       right:
    [ -1280/(1600+1280)  0        1280/1280 ]
    [  0                -800/1200 800/1200  ]
    [  0                 0        1         ]

    [ -0.444444  0        1        ]
    [  0        -0.666666 0.666666 ]
    [  0         0        1        ]

'' Right monitor rotated to inverted ''

    xinput set-prop "Serial Wacom Tablet stylus" --type=float "Coordinate Transformation Matrix" -0.444444 0 1 0 -0.666666 0.666666 0 0 1

Now what happens if we rotate the tablet pc to portrait mode? Note X & Y
swap, becoming 800x1280. Since cos(270) = 0 and sin(270) = -1 you end up
with this matrix.

       right:
    [  0          800/(1600+800) 1600/(1600+800) ]
    [ -1280/1280  0              1280/1280       ]
    [  0          0              1               ]

    [  0  0.333333 0.666666 ]
    [ -1  0        1        ]
    [  0  0        1        ]

'' Right monitor rotated to portrait ''

    xinput set-prop "Serial Wacom Tablet stylus" --type=float "Coordinate Transformation Matrix" 0 0.333333 0.666666 -1 0 1 0 0 1

### Three Monitors

#### Three monitors with the same resolution

\- the simplest case

All three monitors have the same resolution, say 1280x1024. You set up
the linear transform and since we're scaling from 0-1 you'd end up with
this set of matrices.

      left                           center                                     right
    [ 1280/(3*1280) 0         0 ]  [ 1280/(3*1280) 0         1280/(3*1280) ]  [ 1280/(3*1280) 0         (2*1280)/(3*1280) ]
    [ 0             1024/1024 0 ]  [ 0             1024/1024 0             ]  [ 0             1024/1024 0                 ]
    [ 0             0         1 ]  [ 0             0         1             ]  [ 0             0         1                 ]

      left               center                    right
    [ 0.333333 0 0 ]   [ 0.333333 0 0.333333 ]   [ 0.333333 0 0.666666 ]
    [ 0        1 0 ]   [ 0        1 0        ]   [ 0        1 0        ]
    [ 0        0 1 ]   [ 0        0 1        ]   [ 0        0 1        ]

So now the xinput commands to set the device to the desired monitor are
these.

*Left monitor*

    xinput set-prop "Device name" --type=float "Coordinate Transformation Matrix" 0.333333 0 0 0 1 0 0 0 1

*Center monitor*

    xinput set-prop "Device name" --type=float "Coordinate Transformation Matrix" 0.333333 0 0.333333 0 1 0 0 0 1

*Right monitor*

    xinput set-prop "Device name" --type=float "Coordinate Transformation Matrix" 0.333333 0 0.666666 0 1 0 0 0 1

**Note** The xinput command does not last through a restart. See below.

To Have the Settings Last Through a Reboot
------------------------------------------

You have to apply the xinput or xsetwacom command with each restart or
rotation. You should be able to add it to your xsetwacom start up
script, if you have one. Otherwise run it from a convenient start up
script. Sample start up scripts are found in [Wacom Tablet
Configuration](/wiki/Wacom_Tablet_Configuration "wikilink") and [Tablet PC
Configuration](/wiki/Tablet_PC_Configuration "wikilink"). Add the appropriate
command for each device (stylus, eraser, and cursor) to its section.

</noinclude>
