---
title: Button action properties
permalink: wiki/Button_action_properties/
layout: wiki
tags:
 - DeveloperPages
---

[xf86-input-wacom](xf86-input-wacom "wikilink") supports a number of
button actions - sequences of button or key presses that are sent
whenever the matching physical button changes state. The properties that
configure these actions are:

-   "Wacom Button Actions" defines the actions for the physical buttons
    on the tool, in-order.
-   "Wacom Wheel Buttons" defines the actions for the wheels (if any) on
    the tool
-   "Wacom Strip Buttons" defines the actions for the strips (if any) on
    the tool

Property layout
---------------

The three properties work in the same manner. Each property is a list of
Atoms that represent the matching physical button (or move action for
strips/wheels). The Atom must refer to a property on the same device and
contains the sequence of events. The actual events are encoded in the
following format:

-   key press of keycode n: `AC_KEY | AC_KEYBTNPRESS | n`
-   key release of keycode n: `AC_KEY | n`
-   button press of button n: `AC_BUTTON | AC_KEYBTNPRESS | n`
-   button release of button n: `AC_BUTTON| n`

Thus, to set the first button on the pad to send a button 10 event, one
must first create a property (e.g. "button 1 action") and set it to the
value `AC_KEY | AC_KEYBTNPRESS | 10`. Let's say this property has the
Atom 234. The *first* value of the "Wacom Button Actions" property must
then be set to the value 234. The driver keeps track of which buttons
were not released at the end of the action sequence and releases those
buttons whenever the physical button is released.

Example
-------

To manually do this, the following commands can be used:

     $> xinput set-prop "Wacom ISDv4 E6 Pen stylus" "button 1 action" --format=32 --type=int 1572874 # AC_BUTTON|AC_KEYBTNPRESS|10
     $> xinput list-props "Wacom ISDv4 E6 Pen stylus" | grep "button 1 action"
        button 1 action (568):  1572874
     $> xinput set-prop "Wacom ISDv4 E6 Pen stylus" "Wacom Button Actions" 568 0 0 0 0 0 0

[xsetwacom](xsetwacom "wikilink") provides a convenience interface that
does not require manual property creation.
