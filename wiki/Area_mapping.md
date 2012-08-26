---
title: Area mapping
permalink: wiki/Area_mapping/
layout: wiki
tags:
 - HOWTO
---

GTK+ is known to have a bug which can result in an offset between the
pointer and the pen location when running in a multi-head Xinerama
setup. In such cases, it is sometimes possible to work around the bug by
modifying the 'Area' (or, for old versions of linuxwacom, the TopX,
TopY, BottomX, and BottomY) property of the stylus and eraser tools.

Walkthrough
-----------

The wacom driver scales and translates the raw tablet coordinates to fit
onto your desktop. To work around the mapping issue, we can choose a
very specific value for 'Area' which will result in the driver scaling
and translating the tablet coordinates to fit onto a single monitor
instead.

For this to work, you will need to know the resolution of your desktop,
the resolution of the target monitor, and the resolution of your tablet.
To get the third, run the following command:

` xsetwacom get `<id>` area`

The four numbers "a b c d" printed out are the points (a,b) and (c,d)
corresponding to the top-left and bottom-right of your tablet. The
resolution will be (c-a) by (d-b) (e.g. "0 0 60960 45720" would be a
resolution of 60960x45720). If you're running an old version of
linuxwacom, you may need to run the following four commands to get the
same data instead:

` xsetwacom get `<id>` topx`  
` xsetwacom get `<id>` topy`  
` xsetwacom get `<id>` bottomx`  
` xsetwacom get `<id>` bottomy`

Lets assume I want to constrain my tablet to the right monitor in the
following setup:

-   Intuos3 9x12 (resolution: 60960x45720)
-   Left monitor (resolution: 1600x1200, located at 0,0)
-   Right monitor (resolution: 1920x1080, located at 1600,0)

To find the necessary "virtual" tablet width and height, we must first
know the fraction of the desktop width and height the right monitor
takes up. The fractional monitor width will be
(1920/(1600 + 1920)) = 0.54545454, and the fractional monitor height
will be (1080/1200) = 0.9. To make the tablet appear larger than it
actually is to the driver, we'll need to divide the physical resolution
by these fractions to obtain the "virtual" resolution. In this case,
we'd have a virtual width of (60960/0.54545454) = 111760, and a virtual
height of (45720/0.9) = 50800.

To find the origin of our "virtual" tablet, we need to know the fraction
of the desktop the right monitor is offset from the desktop's origin.
The fractional monitor X origin will be
(1600/(1600 + 1920)) = 0.45454545, and the fractional monitor Y origin
will be (0/1200) = 0. We'll offset the virtual tablet's origin by these
fractional amounts, for a virtual X origin of
( − (111760 \* 0.45454545)) =  − 50800 and a virtual Y origin of
( − (50800 \* 0)) = 0

Finally, using the origin and the width of our virtual tablet, we need
to obtain the location of it's bottom-right coordinate (since xsetwacom
uses these two points to define the area). The bottom-right x coordinate
will be ( − 50800 + 111760) = 60960, and the bottom-right y-coordinate
will be (0 + 50800) = 50800. We'll set this area using xsetwacom as
follows:

` xsetwacom set `<stylus id>` area -50800 0 60960 50800`  
` xsetwacom set `<eraser id>` area -50800 0 60960 50800`

Again, if you're running an old version of linuxwacom, you may need to
use 'set' with each of TopX, TopY, BottomX, and BottomY instead to save
your user-defined area.

### To Have the Settings Last Through a Reboot

You have to apply the xsetwacom command with each restart or rotation.
You should be able to add it to your xsetwacom start up script, if you
have one. Otherwise run it from a convenient start up script. A sample
start up script is shown in [Sample Runtime
Script](/wiki/Tablet_Configuration#Sample_Runtime_Script "wikilink"). Add the
appropriate command for each device (stylus, eraser, and cursor) to its
section.

Python implementation
---------------------

The short Python program below can be copy/pasted and run to determine
the correct numbers. You'll need to set the tablet and monitor
resolutions to match your own setup.

    #!/usr/bin/env python

    from __future__ import division
    def main():
        tablet = Device("60960x45720")         # Replace with the resolution of your tablet

        monitor_0 = Device("1600x1200")        # Replace with the resolution of your left screen
        monitor_1 = Device("1920x1080")        # Replace with the resolution of your right screen
        monitors = [monitor_0, monitor_1]

        monitor_1.place_right_of(monitor_0)
        desktop = bounding_box(monitors)

        area = map_tablet_onto(tablet, desktop, monitor_1) # Specify monitor to map tablet to here

        cmd  = "xsetwacom set <id> area %d %d %d %d" % (area.p1[0], area.p1[1], area.p2[0], area.p2[1])
        print(cmd)

    #######################################################################
    ## Do not edit below this line.
    ##

    class Vector:
        def __init__(self, *data):
            self.data = data
        def __repr__(self):
            return repr(self.data) 
        def __add__(self, other):
            return Vector( *[a+b for a,b in zip(self.data, other.data) ] )  
        def __sub__(self, other):
            return Vector( *[a-b for a,b in zip(self.data, other.data) ] )
        def __mul__(self, other):
            return Vector( *[a*b for a,b in zip(self.data, other.data) ] )
        def __truediv__(self, other):
            return Vector( *[1.0*a/b for a,b in zip(self.data, other.data) ] )
        def __iter__(self):
            return iter(self.data)
        def __getitem__(self, index):
            return self.data[index]

    class Rectangle:
        def __init__(self, p1=(0,0), p2=(0,0)):
            self.p1, self.p2 = Vector(*p1), Vector(*p2)
        def __repr__(self):
            return repr((self.p1, self.p2))
        def __add__(self, point):
            return Rectangle(self.p1 + point, self.p2 + point)
        def __sub__(self, point):
            return Rectangle(self.p1 - point,  self.p2 - point)
        def getSize(self):
            return self.p2 - self.p1
        def setSize(self, size):
            self.p2 = self.p1 + size
        def getOrigin(self):
            return self.p1
        def setOrigin(self, point):
            self.p2, self.p1 = self.size + point, point
        size = property(getSize, setSize)
        origin = property(getOrigin, setOrigin)

    class Device(Rectangle):
        def __init__(self, resolution):
            x, y = resolution.split("x")
            Rectangle.__init__(self, (0,0), Vector(int(x), int(y)))
        def place_right_of(self, other):
            self.setOrigin(other.origin + Vector(other.size[0], 0))
        def place_left_of(self, other):
            self.setOrigin(other.origin - Vector(self.size[0], 0))
        def place_above(self, other):
            self.setOrigin(other.origin - Vector(0, self.size[1]))
        def place_below(self, other):
            self.setOrigin(other.origin + Vector(0, other.size[1]))

    def bounding_box(rectangles):
        points = [rect.p1 for rect in rectangles]
        min_x = min([x for x,y in points])
        min_y = min([y for x,y in points])

        points = [rect.p2 for rect in rectangles]
        max_x = max([x for x,y in points])
        max_y = max([y for x,y in points])

        return Rectangle((min_x, min_y), (max_x, max_y))

    def normalize(desktop, monitor):
        rect = Rectangle([ (monitor.p1 - desktop.p1)[i] / desktop.size[i] for i in [0,1] ])
        rect.setSize(monitor.size / desktop.size)
        return rect

    def map_tablet_onto(tablet, desktop, monitor):
        norm = normalize(desktop, monitor)
        rect = Rectangle()
        rect.setSize(tablet.size / norm.size)
        rect.setOrigin ( tablet.origin - (rect.size * norm.origin) )
        return rect


    if __name__=="__main__":
        main()
