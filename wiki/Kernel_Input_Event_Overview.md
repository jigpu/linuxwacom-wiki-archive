---
title: Kernel Input Event Overview
permalink: wiki/Kernel_Input_Event_Overview/
layout: wiki
tags:
 - DeveloperPages
---

Simple/Generic Tablet Input Event Overview
------------------------------------------

There are simple 1 tool tablets supported by Linux Kernel. The Wacom
tablets never fall in to this category but its useful to understand them
first. These generic tablets will report events over standard Linux
/dev/input/eventX interfaces.

Tablets have a concept were a tool is in proximity of pad and can start
reporting valid X/Y coordinates. They also can report when the tool is
physically touching the device. The approach used to indicate this is
tablets will send a BTN\_TOOL\_PEN event with value of non-zero any time
a tool comes into proximity of the tablet and 0 when out of proximity.
The BTN\_TOUCH event is then used to determine when the tool is touching
the tablet. The ABS\_PRESSURE event is optional in this case but can be
sent to complement the BTN\_TOUCH.

When ABS\_PRESSURE, BTN\_TOUCH, and BTN\_TOOL\_PEN are all 3 supported,
the meaning of BTN\_TOUCH can sometimes change to same meaning as
BTN\_TOOL\_PEN. In this case, it is safest to ignore BTN\_TOUCH value.

There are cases of simple tablets were the hardware can not report when
in proximity of tablet and can only report when physically touching.
Applications generally do not need to worry about this. To applications,
the events reported will just never be a combination where the tool is
in proximity but not touching tablet.

Most user applications detect difference between a tablet and things
such as touchpads on /dev/input/eventX is by looking for support for
either BTN\_STYLUS or BTN\_TOOL\_PEN event. It has to check for both to
handle a historical case of some tablets never reporting BTN\_TOOL\_PEN.

The other common events tablets will send are ABS\_X and ABS\_Y to
indicate the location of pen on tablet and BTN\_STYLUS when a button is
pressed that is located on the stylus/pen tool itself. ABS\_X, ABS\_Y,
ABS\_PRESSURE, and BTN\_STYLUS values should always be ignored by
applications when driver reports tool is not in proximity to prevent
unwanted behavior because some drivers will report values even while
tool is out of proximity.

There are also various buttons such as BTN\_LEFT/BTN\_RIGHT/BTN\_1/etc
that tablets can report regardless of the state tools These represent
buttons that exist on the tablet itself (as apposed to on stylus/pen
tool) and user expects these to work regardless of what tool is doing.

Here is a hypothetical tablet that uses BTN\_TOOL\_PEN approach. In this
example, BTN\_TOOL\_PEN and BTN\_TOUCH most likely are always same
value. ABS\_PRESSURE needs to be looked at to detect touch.

-   BTN\_TOOL\_PEN
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_TOUCH
-   BTN\_LEFT
-   BTN\_RIGHT

Here is one last hypothetical generic tablet that uses BTN\_TOOL\_PEN
approach. Here BTN\_TOUCH determines physical touch.

-   BTN\_TOOL\_PEN
    -   ABS\_X
    -   ABS\_Y
    -   BTN\_TOUCH
-   BTN\_LEFT
-   BTN\_RIGHT

Wacom Tablet Input Event Overview
---------------------------------

Wacom tablets support multiple tools that can report events over a
single Linux /dev/input/eventX device and these tools often share
similar characteristics. The easiest example is a tablet can have a
stylus with both a pen tool on one end and an eraser tool on the other
end and both these tools have X/Y and Pressure values.

The pen and eraser show a form of physical mutual exclusion since you
must flip the stylus to use the other tool. This flip will force old
tool out of proximity and new tool to come into proximity. For most of
discussion, we will ignore the case where user owns multiple stylus.

There are cases where user expects multiple tools to work concurrently
though. The wacom kernel drivers break tools up into 3 groups. When 1
group can support more then 1 tool then the wacom kernel driver will
only allow 1 tool per group to ever be in proximity. Different tablets
support different combinations of tool groups.

1.  Stylus or Mouse Tool (Pen, Eraser, Mouse, etc)
2.  Mouse Tool of Intuos 1 or 2 (when \#1 is Stylus Tool)
3.  Buttons located on Wacom Tablet (not buttons on stylus)

When the wacom kernel driver reports data for stylus, it reports them
much like the generic tablets do (BTN\_TOOL\_\* for proximity). In
addition, the kernel will always send a BTN\_TOUCH that tracks the
BTN\_TOOL\_\* and uses ABS\_PRESSURE as a more accurate gauge of when
physical touch is occurring.

Its important to understand that the Linux Input layer will filter out
duplicate events and does not understand concept of multiple tools.
Unless something is done, if tool A leaves proximity at ABS\_X of 10 and
tool B enters proximity at ABS\_X of 10 then user land would never be
inform tool B is at ABS\_X of 10. Wacom kernel drivers work around this
issue by forcing all events to a known starting value (zero) each time a
tool leaves proximity and user land can assume this starting value when
it first enters proximity.

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

For Protocol 4 tablets, the Stylus and Mouse events are hard code to
serial \# 1 and Pad button events are hard coded to 0xf0. Its OK that
all tools in group 1 (Stylus and Mouse) are assigned to the same serial
\# because they are mutually exclusive.

For Protocol 5 devices (Intuos and Cintiq series), the Pad device is
assigned to channel 0xffffffff (-1) and all other tools are assigned a
unique serial \# read from tool by hardware. Intous and Intuos 2 tablets
support up to 2 tools within group 1 or 2 to be in proximity at same
time which means when switching between two tools within group 1 then
applications must account for Linux event filtering.

Its important to note that buttons located on the tablet pad are treated
as a tool and send an BTN\_TOOL\_FINGER event while generic tablets just
send those button events as needed.

The following shows events returned from a hypothetical Wacom tablet and
indentation shows implied hierarchy of events. When a BTN\_TOOL\_\* goes
out of proximity, a well behaved wacom kernel driver will also reset all
items under its hierarchy to a known fixed value (zero). Also of note,
the value of MSC\_SERIAL is never reset to a neutral values, such as
zero, even though its listed under the hierarchy. Any time data for new
tool is sent, the new MSC\_SERIAL will always be sent within that same
EV\_SYNC window. This is important to know for the case when two tools
can be in proximity at the same time; such as the pen tool and "finger"
tool (buttons on tablet pad).

-   BTN\_TOOL\_PEN
    -   MSC\_SERIAL (fixed to 1 to represent group 1)
    -   ABS\_MISC
    -   BTN\_TOUCH
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_TOOL\_ERASER
    -   MSC\_SERIAL (fixed to 1 to represent group 1)
    -   ABS\_MISC
    -   BTN\_TOUCH
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_TOOL\_FINGER
    -   MSC\_SERIAL (fixed to 0xf0 to represent group 3).
    -   ABS\_MISC
    -   BTN\_LEFT
    -   BTN\_RIGHT

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
interface around Linux 2.6.37 timeframe. Tablet PC's currently have no
timeframe when they will be switched over.

There exists a version of Bamboo kernel drivers in linuxwacom tarballs
were the touchpad acts exactly like Tablet PC's. The only difference is
that they also report the tablet buttons as a 3rd tool in addition to
the 2 finger tools.

Hypothetical Wacom Tablet using Tablet PC method of sending concurrent
finger data:

-   BTN\_TOOL\_DOUBLETAP
    -   MSC\_SERIAL (fixed to 1 to represent group 1)
    -   ABS\_MISC
    -   BTN\_TOUCH
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_TOOL\_TRIPLETAP
    -   MSC\_SERIAL (fixed to 2 to represent group 2)
    -   ABS\_MISC
    -   BTN\_TOUCH
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_TOOL\_FINGER
    -   MSC\_SERIAL (fixed to 0xf0 to represent group 3)
    -   ABS\_MISC
    -   BTN\_LEFT
    -   BTN\_RIGHT

You can compare this to synaptics-like events handled by
xf86-input-synaptics or xf86-input-evdev and their different event
hierarchy:

-   BTN\_TOOL\_FINGER
    -   BTN\_TOUCH
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_TOOL\_DOUBLETAP
    -   BTN\_TOUCH
    -   ABS\_X
    -   ABS\_Y
    -   ABS\_PRESSURE
-   BTN\_LEFT
-   BTN\_RIGHT

Only 1 of BTN\_TOOL\_FINGER or BTN\_TOOL\_DOUBLETAP can be in proximity
at the same time. Its related ABS\_X, ABS\_Y, and ABS\_PRESSURE are
almost always a value related to first finger touching the touchpad but
changes in meaning can and do happen when switching between tool types.

In addition, newer kernels can add Multi-Touch protocol events to above
synaptic-like events.

-   ABS\_MT\_SLOT
    -   ABS\_MT\_TRACKING\_ID
    -   ABS\_MT\_POSITION\_X
    -   ABS\_MT\_POSITION\_Y
    -   ABS\_MT\_PRESSURE

These work similar to older events but event filtering behavior is
modified. Instead of filtering events based on previous sent value (if
ABS\_X=10 and send ABS\_X=10 during tool switch then its filtered), the
filtering is now based on previous value of a specific slot. For
example, if slot 0's ABS\_MT\_POSITION\_X=10 and slot 1's
ABS\_MT\_POSITION\_X=20 during previous sends then here is example
filtering behavior:

-   send(ABS\_MT\_SLOT,0)-&gt; sends ABS\_MT\_SLOT=0
-   send(ABS\_MT\_POSITION\_X,10) -&gt; event is filtered
-   send(ABS\_MT\_SLOT,1) -&gt; sends ABS\_MT\_SLOT=1
-   send(ABS\_MT\_POSITION\_X,10) -&gt; sends ABS\_MT\_POSITION\_X=10
