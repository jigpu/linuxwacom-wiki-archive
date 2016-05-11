---
title: Consumer Tablet ExpressKey Mapping Issue
permalink: wiki/Consumer_Tablet_ExpressKey_Mapping_Issue/
layout: wiki
---

This page applies to, among others, the following devices:

`"Wacom Bamboo 16FG 6x8", 0xDF`  
`"Wacom Intuos PT S", 0x302`  
`"Wacom Intuos PT M", 0x303`  
`"Wacom Intuos S", 0x30E`  
`"Wacom Intuos P M", 0x323`  
`"Wacom Intuos S 2",0x33B`  
`"Wacom Intuos PT S 2", 0x33C`  
`"Wacom Intuos P M 2", 0x33D`  
`"Wacom Intuos PT M 2", 0x33E`  

History
-------

Wacom Branded devices have historically been divided into 2 categories,
professional (e.g. Intuos Pro) and consumer (e.g. Intuos 2 (2015)).
Because of the different target users of these devices, Wacom defines
the default button mapping of each device differently.

Whereas the buttons for the professional devices are numbered
sequentially, consumer devices are defined as Left Button, Right Button,
Back Button, and Forward Button. Because Wacom has full control over the
Wacom Windows driver and control panel, remapping the directional
buttons to other values is more straightforward. Remapping the
directional buttons on Linux requires navigating the input stack. To
replicate the default Windows button mapping, the Wacom Kernel driver
and Wacom X driver attempt to pass along the same default directional
button mappings, but GNOME does not watch for the consumer device's
button definitions.

For four button devices, these consumer tablet ExpressKey buttons are
given the values 1,3,8, and 9 when leaving the Wacom X driver. In
contrast, the X driver passes along sequential numerical button mappings
(1,2,3,8,9,...) for the professional tablet series (e.g. Intuos Pro,
Intuos 4, Cintiq, etc.). A relevant side note is that GNOME [works
around](https://git.gnome.org/browse/gnome-settings-daemon/tree/plugins/wacom/gsd-wacom-device.c#n2047)
the button numbering gap for buttons 4 to 7 introduced by the X driver.

GNOME
-----

gnome-settings-daemon (gsd) and gnome-control-panel (gcc) work closely
together. They provide the button mappings seen in the Wacom Gnome
control panel (used in Fedora and RHEL) as well as the derivative Unity
Control Panel (used in Ubuntu). The maintainers of gsd/gcc
understandably do not wish to support the awkward button mapping of the
consumer buttons. See the section "Relevant Discussions" below for more
details.

### Solutions - How to assign keyboard shortcuts

The most direct way to assign these buttons is to use
[xsetwacom](xsetwacom "wikilink"). See [Tablet
Configuration](/wiki/Tablet_Configuration "wikilink") for instructions on how
to use xsetwacom. Using xsetwacom, you can bypass GNOME and set the
meaning of the buttons one layer lower in the stack. You have to apply
the xinput or xsetwacom command with each restart, so you may want to
use a startup script like the one discussed in [Tablet
Configuration](/wiki/Tablet_Configuration#Sample_Runtime_Script "wikilink").

Technical details
-----------------

The Consumer devices end up with the button numbers of 1,3,8, and 9 due
to the translation of BTN\_LEFT, BTN\_RIGHT, BTN\_BACK, and BTN\_FORWARD
(not respectively). It is possible, though potentially frustrating, to
assign buttons 1,3, and 8 using GNOME's interface. For those buttons,
the button representations on screen won't line up with the buttons on
the physical tablet, but can still be assigned.

Relevant fix discussions:  
<https://sourceforge.net/p/linuxwacom/mailman/message/32096190/>  
<https://sourceforge.net/p/linuxwacom/bugs/244/>  
Relevant Developer Discussions:  
<https://www.mail-archive.com/linuxwacom-devel%40lists.sourceforge.net/msg04896.html>  
<https://www.mail-archive.com/linuxwacom-devel%40lists.sourceforge.net/msg04918.html>  
<https://www.mail-archive.com/linuxwacom-devel%40lists.sourceforge.net/msg05980.html>  
<http://sourceforge.net/p/linuxwacom/mailman/message/32091384/>  
<http://who-t.blogspot.com/2016/04/libinput-and-graphics-tablet-pad-support.html>  
