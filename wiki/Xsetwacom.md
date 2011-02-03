---
title: Xsetwacom
permalink: wiki/Xsetwacom/
layout: wiki
tags:
 - HOWTO
---

xsetwacom is a commandline utility to change wacom driver settings at
runtime. This includes direct settings of driver properties but also
several 'indirect' settings (i.e. the xsetwacom command does not relate
directly to a single property). For a detailed description of
xsetwacom's commands and options, please refer to the man page. This
page provides a general overview and describes some of the
[quirks](#Quirks "wikilink") you may encounter.

**A note to developers: do not rely on xsetwacom, as it's UI will change
without warning.**

Usage
=====

The four commands you will need are:

` xsetwacom list`

To list the applicable devices. The usual invocation to get a parameter
is:

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
