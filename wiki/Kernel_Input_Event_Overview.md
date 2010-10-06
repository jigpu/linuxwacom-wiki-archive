---
title: Kernel Input Event Overview
permalink: wiki/Kernel_Input_Event_Overview/
layout: wiki
---

Simple/Generic Tablet Input Event Overview
------------------------------------------

There are simple 1 tool tablets supported by Linux Kernel. The Wacom
tablets never fall in to this category but its useful to understand them
first.

These generic tablets will report events over standard Linux
/dev/input/event0 interfaces. They will send a BTN\_TOUCH with value of
non-zero any time a tool comes into proximity of the tablet and 0 when
out of proximity. The ABS\_PRESSURE event is used to determine when the
tool is touching the tablet. The most common other events they will send
are ABS\_X and ABS\_Y to indicate the location of pen on tablet and
BTN\_STYLUS when a button is pressed located on the stylus itself. All
these events only make sense when BTN\_TOUCH is a non-zero value.

Most user applications detect difference between a tablet
/dev/input/event0 and other types of inputs by looking for support for
either BTN\_STYLUS or BTN\_TOOL\_PEN events.

Of those two, BTN\_TOOL\_PEN exists purely to inform userland this
tablet supports pen tools. Its value generally tracks value of
BTN\_TOUCH.

There are also various buttons such as BTN\_LEFT/BTN\_RIGHT/BTN\_1/etc
that tablets can report regardless of the state of BTN\_TOUCH. These
represent buttons that exist on tablet itself and user expects these to
work regardless of a tool being used.

Wacom Tablet Input Event Overview
---------------------------------

Wacom tablets support multiple tools that can report events over a
single Linux /dev/input/event0 device and these tools often share
similar characteristics. The easiest example is a tablet can have a
stylus with both a pen tool on one end and an eraser tool on the other
end and both these tools have X/Y and Pressure values.

The pen and eraser show a form of physical mutual exclusion since you
must flip the stylus to use the other tool. This flip will force old
tool out of proximity and new tool to come into proximity. For this
discussion, we will ignore the case were user owns multiple stylus.

There are cases where user expects multiple tools to work concurrently
though. The kernel drivers break tools up into 3 groups. When a group
supports more then 1 tool then the kernel will only allow 1 tool per
group to ever be in proximity. Different tablets support different
combinations of tool groups.

-   Stylus or Mouse Tool (Pen, Eraser, Mouse, etc)
-   Mouse Tool of Intuos 1 or 2 (when \#1 is Stylus Tool)
-   Buttons located on Wacom Tablet (not buttons on stylus)

When the kernel reports data for stylus, it reports them much like the
generic tablets do (BTN\_TOUCH for proximity). In addition, the kernel
will always send a BTN\_TOOL\_\* that tracks BTN\_TOUCH to indicate to
user which tool the events are currently be reported for.

Its important to understand that the Linux Input layer will filter out
duplicate events and does not understand concept of multiple tools.
Unless something is done, if tool A leaves proximity at ABS\_X of 10 and
tool B enters proximity at ABS\_X of 10 then user land would never be
inform tool B is at ABS\_X of 10. Wacom kernel drivers work around this
issue by forcing all events to a known starting value (zero) each time a
tool leaves proximity and user land is required to assuming this
starting value when it first enters proximity.

If you review events supported by the above 3 groups of tools, you'll
notice tools within 1 group share common events but tools in different
groups share no events. This is why 1 tool in each of the 3 groups can
be in proximity at the same time; event filtering will not occur between
groups and so kernel does not need to reset to known starting value when
switching between groups. For example, Stylus in group 1 support
Absolute X values (ABS\_X) while mice in group 2 support Relative X
values (REL\_X).

In addition to the BTN\_TOOL\_\* events for informing user land what
tool the current events being sent belong to, there is a MSC\_SERIAL
event that contains a serial \# to aid in tracking current tool as well
as a ABS\_MISC which is a hard code device ID. Of these two, the
MSC\_SERIAL is the most useful to user land.

For all devices except Intous 1 and 2, the Stylus and Mouse events are
hard code to serial \# 1 and any Pad buttons are hard coded to
0xffffffff (-1) for Intous series and 0xf0 for all others. Its OK to
assign Stylus and Mouse to same serial \# because they are mutually
exclusive.

For Intous devices, the Pad data is assigned to channel 0xffffffff (-1)
and the Stylus/Mouse data are assigned a dynamic serial \# as reported
by hardware.

Its important to note that buttons located on tablet are treated as a
tool and send BTN\_TOOL\_FINGER while generic tablets just send those
button events as needed. xf86-input-wacom currently has issues with
generic tablets approach.

Touchpad Overview
-----------------

Recently, Wacom has begun to ship touchpad-like devices. The first were
Tablet PC's that supported touch on the screens. The next were in the
Bamboo series of tablets.

All current touchpad-like events are reported over its own
/dev/input/event0 device. For Tablet PC's and Bamboo tablets that also
support Stylus, there will be another /dev/input/event device and it
will report stylus tools using description for tablets.

The original Tablet PC borrowed the multiplexing scheme used by tablets.
It broke the touchpad data into two sets. 1) Finger 1 related events and
2) Finger 2 related events.

A synaptics-like touchpad input device would normally send BTN\_TOUCH to
indicate when one or more fingers are touching, BTN\_TOOL\_DOUBLETAP to
indicate when 2 or more fingers are touching and BTN\_TOOL\_TRIPLETAP to
indicate when 3 or more fingers are touching. For example, 1st finger
touch will send BTN\_TOUCH = 1, 2nd finger will send
BTN\_TOOL\_DOUBLETAP = 1, release 2nd finger will send
BTN\_TOOL\_DOUBLETAP = 0, and release 1st finger will send BTN\_TOUCH =
0. Synaptics-like devices only have 1 set of ABS\_X/ABS\_Y values for
all fingers though.

To allow multiple finger data to be reported, Wacom Tablet PC's treat
fingers like tablet tools and follow tablet descriptions if you treat
BTN\_TOOL\_DOUBLETAP to mean "1st finger tool" and BTN\_TOOL\_TRIPLETAP
to mean "2nd finger tool". One important difference is that even though
Finger 1 (BTN\_TOOL\_DOUBLETAP) and Finger 2 (BTN\_TOOL\_TRIPLETAP)
share common events, the kernel driver still lets both fingers be in
proximity at the same time. This mean Linux Input layer filtering can
cause events to be be lost and this can be seen when gesture logic gets
confused from lost data.

The up side to this approach is since they are re-using tablet serial
\#/multiplexing schemes, these touchpad-like devices can be handled by
xf86-input-wacom.

The long term solution to lost events is to eventually change to using
the Linux Input layer MT (multi-touch) support that has been added to
recent kernels to work around this now common to all multi-touch
hardware filtering issue.

The Bamboo touchpad device is scheduled to be switched over to this
interface around Linux 2.6.37 timeframe. Tablet PC's for no current
timeframe when they will be switched over.

There exists a version of Bamboo kernel drivers in linuxwacom tarballs
that the touchpad acts exactly like Tablet PC's. The only difference is
that they also report the tablet buttons as a 3rd tool in addition to
the 2 finger tools.