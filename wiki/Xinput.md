---
title: Xinput
permalink: wiki/Xinput/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

<noinclude>

The [xinput](http://cgit.freedesktop.org/xorg/app/xinput) commandline
tool is the most important tool to get information about X input devices
and their properties. This page describes the most common usage patterns
for xinput.

Listing devices
===============

</noinclude>

The [xinput](xinput "wikilink") parameter *list* will list all currently
available devices and their device name. Use this to find the device
name and device ID of your device. Below you can see the example output
on a machine with a Intuos4 tablet attached:

    $ > xinput --list
    ⎡ Virtual core pointer                      id=2    [master pointer  (3)]
    ⎜   ↳ Virtual core XTEST pointer                id=4    [slave  pointer  (2)]
    ⎜   ↳ SynPS/2 Synaptics TouchPad                id=14   [slave  pointer  (2)]
    ⎜   ↳ Wacom Intuos4 6x9 eraser                  id=17   [slave  pointer  (2)]
    ⎜   ↳ Wacom Intuos4 6x9 cursor                  id=18   [slave  pointer  (2)]
    ⎜   ↳ Wacom Intuos4 6x9 pad                     id=19   [slave  pointer  (2)]
    ⎜   ↳ Wacom Intuos4 6x9 stylus                  id=20   [slave  pointer  (2)]
    ⎣ Virtual core keyboard                     id=3    [master keyboard (2)]
        ↳ Virtual core XTEST keyboard               id=5    [slave  keyboard (3)]
        ↳ AT Translated Set 2 keyboard              id=13   [slave  keyboard (3)]

The device name in quotes should be used in other
[xinput](xinput "wikilink") commands to retrieve or apply settings from
or to a device.

<noinclude>

Listing device-specific information
===================================

</noinclude>

Detailed information about a specific device can be shown by specifying
the device name (or the device ID):

    $ > xinput --list "Wacom Intuos4 6x9 eraser"

<noinclude> For example, looking at the eraser tool-specific values
reveals this:

    $ > xinput --list "Wacom Intuos4 6x9 eraser"
    Wacom Intuos4 6x9 eraser                    id=17   [slave  pointer  (2)]
        Reporting 8 classes:
            Class originated from: 17
            Buttons supported: 9
            Button labels: None None None None None None None None None
            Button state:
            Class originated from: 17
            Keycodes supported: 248
            Class originated from: 17
            Detail for Valuator 0:
              Label: Abs X
              Range: 0.000000 - 44704.000000
              Resolution: 200000 units/m
              Mode: absolute
              Current value: 0.000000
            Class originated from: 17
            Detail for Valuator 1:
              Label: Abs Y
              Range: 0.000000 - 27940.000000
              Resolution: 200000 units/m
              Mode: absolute
              Current value: 0.000000
            Class originated from: 17
            Detail for Valuator 2:
              Label: Abs Pressure
              Range: 0.000000 - 2048.000000
              Resolution: 1 units/m
              Mode: absolute
              Current value: 0.000000
            Class originated from: 17
            Detail for Valuator 3:
              Label: Abs Tilt X
              Range: -64.000000 - 63.000000
              Resolution: 1 units/m
              Mode: absolute
              Current value: 0.000000
            Class originated from: 17
            Detail for Valuator 4:
              Label: Abs Tilt Y
              Range: -64.000000 - 63.000000
              Resolution: 1 units/m
              Mode: absolute
              Current value: 0.000000
            Class originated from: 17
            Detail for Valuator 5:
              Label: Abs Tilt Y
              Range: -64.000000 - 63.000000
              Resolution: 1 units/m
              Mode: absolute
              Current value: 0.000000

The above shows 9 buttons, and several axes with their label and their
min/max values. For example, the absolute x axis has a min/max of 0 -
44704 units, with 20000 units/m. </noinclude>
