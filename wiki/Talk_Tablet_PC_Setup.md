---
title: Talk:Tablet PC Setup
permalink: wiki/Talk:Tablet_PC_Setup/
layout: wiki
---

2-16-11 Addressed with: Turning touch off can be convient when working
in a graphics program, so you don't have to worry about accidentally
issuing a command, or worse, drawing a line if you incidentally touch
the tablet's screen when not holding the stylus. Additionally some
serial tablet pc's have issues with extra lines being drawn in
applications such as Xournal, when touch is on while writing with the
stylus.

\[The following script depends on the xsetwacom get and set commands.\]
The get command determines whether touch is on or off while the two set
commands turn touch on or off. \[The "device name" for touch is found by
using 'xsetwacom list' as mentioned in the script comments.\]

For the N-trig: N-Trigs can also have the issue of extra lines being
drawn even when using the stylus if touch is on. ... This script works
in a different manner from the one above. When first toggled it issues
the xinput touch disable command (0) and then creates a file called
touch\_off in the /tmp directory. When toggled again it checks for the
presence of the touch\_off file and if present issues the xinput touch
enabled (1) command, followed by the deletion of the file.

2-15-11 Why do we have Tablet PC Configuration and Tablet PC Setup?

The thought was that if you did not decide to delete the script pages I
would add the individual models to Tablet PC Setup and link them to
their sections of Tablet PC Configuration. They could be merged but I
felt that would make Tablet PC Setup unwieldy. Same applies to Wacom
Tablet. Waltop is the exception because there isn't much variability
between models that I can see.

Why is this not part of the Rotation page?

Re: the tablet pc auto-rotation script. It's a case of general
applicability. The xrandr match line in the tablet pc rotation script
has been in use for a little over two years and I presume has been
tested by thousands of users. I base that on the fact the HOW TO it has
been posted on has had over 78,000 hits to date. Provided your video
chipset/driver supports rotation it seems to work for everyone. Given
that, and the fact it is a simple "modular" script I felt it belonged in
Rotation.

I can only guarantee the tablet pc auto-rotation script works for HP's
and Thinkpad's. I know some other Brand models report tablet state to
/etc/acpi-somewhere. Ubuntu still has a rotation script in there for
them. So I feel confident other tablet pc's could potentially use it.

But I have no objection to moving it to Rotation if desired. - Favux
