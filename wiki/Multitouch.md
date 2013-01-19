---
title: Multitouch
permalink: wiki/Multitouch/
layout: wiki
tags:
 - HOWTO
---

Wacom has provided multitouch capable tablet digitizer/touchpad and
tablet PC digitizer/touchscreen combinations for several years now.
Initially multitouch was limited to two finger touch (2FGT). Wacom has
since introduced more capable touch hardware. The third generation
BambooPT and Intuos5 touch tablets have 16FGT, while the Cintiq 24HD
touch has 10FGT. The tablet PCs with Product IDs 0x100, 0x101, and
0x4001 all have 10FGT.

With the addition of the mt.h (multi-touch header) to the 2.6.38 kernel
and the multitouch code to the 1.12 X Server (X Input 2.2) the plumbing
is in place for true linux multitouch that can use the full capabilities
of the Wacom touch hardware.

The xf86-input-wacom driver supports 2FGT gestures as a legacy feature
from linuxwacom grandfathered in. To prevent proliferation of driver
specific gestues no new in-driver gestures are planned. The thinking is
it is far more useful to have touch gestures abstracted from input
drivers and supported by a gesture engine that can be used by all
drivers and multitouch aware applications. Accordingly starting with
xf86-input-wacom-0.18.0 when you disable xf86-input-wacom's default
[in-driver 2FGT
support](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/xf86-input-wacom;a=commit;h=0d164e66cbb7119d68d2008242f0ec2cca2202a7)
all the hardware-tracked fingers pass up to the X server. That allows
all of the hardware reported touch contacts to be handled by the new
multitouch through X Server features (XI2.2; ABI &gt;= 16) and
supporting drivers.

While the plumbing is in place either Desktop specific or cross-Desktop
gesture engines have to be written and multitouch features added to
toolkits and applications. Multitouch is still in a state of flux so
this information has to be viewed as preliminary and subject to change.
Any contributions re multitouch to this wiki page are appreciated.

Disable xf86-input-wacom In-driver 2FGT Gestures
------------------------------------------------

As mentioned above starting with xf86-input-wacom-0.18.0 when in-driver
2FGT gestures are disabled by turning the Gesture parameter off the
touch contacts are now passed to the X Server. This can be done in a
custom .conf file as in this example
[52-wacom.conf](/wiki/Xorg.conf.d#Example_Dependent_Device_Static_Configuration "wikilink"):

        Option "Gesture" "off"

or using xsetwacom.

    xsetwacom set "Wacom Bamboo 16FG 4x5 Finger touch" Gesture off

Of course you would use your touch <device name> from **xinput list**.

Two Finger Gestures
-------------------

When first using a Wacom tablet's "touchpad" features you have to be
patient because there is somewhat of a learning curve. It does not have
the same "feel" as a laptop touchpad. One example is the Wacom scrolling
direction is reversed from Synaptic touchpad scrolling. Visualize it as
grabbing the page with two fingers then sliding it up or down or left
and right.

Once you've developed a feel for Wacom touch you can slowly try varying
some of the xsetwacom parameters, see **man xsetwacom**. In addition you
can also change the "feel" of your tablet's touch by adjusting the
xinput Acceleration Profile algorithm and its Device Acceleration
parameters. Details are available in this FreeDesktop wiki article
[PointerAcceleration](http://xorg.freedesktop.org/wiki/Development/Documentation/PointerAcceleration).

### Gesture List

**Single finger (1FG)**

-   Left click - tap, actual click happens after TapTime period elapses.
-   Left click drag - tap to select, quickly (within TapTime period)
    touch selection again. Then drag. Available only on touchpads.

**Two finger (2FG)**

-   Right click - one finger down + tap second finger
-   Vertical scroll - two fingers side by side (e.g. index + second),
    move up or down holding constant distance between fingers.
-   Horizontal scroll - two fingers side by side (e.g. index + second),
    move left or right holding constant distance between fingers.
-   Zoom in - two separated fingers side by side (e.g. thumb + index),
    bring together i.e. pinch.
-   Zoom out - two fingers side by side (e.g. thumb + index), spread.

Available in **man wacom**.

### Xinput Acceleration Profile

To view Device Accel parameters being applied to your Wacom touch device
first enter **xinput list** in a terminal. Using the <device name> or ID
\# returned for touch enter in a terminal:

    xinput list-props "<device name>" or ID #

The most important parameter is the Device Accel Profile since that
selects the algorithm that is applied to the pointer. The other three
Device Accel parameters (Constant Deceleration, Adaptive Deceleration,
and Velocity Scaling) then modify the algorithm. See the
"AccelerationProfile \[integer\]" section in the wiki article linked
above. The default Device Accel Profile is 0.

If you find the **pointer arrow over accelerated** with the default
Device Accel Profile of 0, consider runtime xinput commands using the
appropriate "Device Accel" parameters to compensate. Over acceleration
can be caused by a couple of things. When the tablet is initialised as
relative device, the X server doesn't have an axis range to help in
scaling the movement. Also Wacom touch has a higher resolution than the
average mouse/synaptic touchpad, so it sends more events (a higher data
rate). So it can be too fast for the default mouse settings. Which is
why increasing Constant Deceleration makes sense.

The defaults are 1, 1, and 10 for *Constant Deceleration*, *Adaptive
Deceleration*, and *Velocity Scaling*. For an example let's use a Wacom
Pen and Touch and adjust the settings to get less pointer acceleration
and achieve a more acceptable feel:

    xinput set-prop "Wacom BambooFun 2FG 4x5 Finger touch" --type=float "Device Accel Constant Deceleration" 1.250000
    xinput set-prop "Wacom BambooFun 2FG 4x5 Finger touch" --type=float "Device Accel Adaptive Deceleration" 1.150000
    xinput set-prop "Wacom BambooFun 2FG 4x5 Finger touch" --type=float "Device Accel Velocity Scaling" 10.000000

These xinput commands can be added to the touch section of a xsetwacom
script, if you are using one.

Multi-finger Gestures
---------------------

### GNOME Shell Desktop

Tomeu Vizoso on [his
blog](http://blog.tomeuvizoso.net/2012/09/multi-touch-gestures-in-gnome-shell.html)
posted example code for a Mutter Gesture Engine plug-in. It includes
gesture recognizers for pan, zoom and rotate actions, and he says
creating new recognizers is pretty easy. He put the Mutter branch with
the plug-in up for review on [bugzilla
\#683382](https://bugzilla.gnome.org/show_bug.cgi?id=683382) The bug is
considered as *resolved obsolete* because Mutter has been ported to XI2.
So Gnome 3.8, due out 3-27-13, should have more multitouch support in
Mutter/GNOME Shell.

#### GTK+

Because the multitouch Xorg patches were posted and merged to master
before GTK+ 3.4 came out, a minimal touch-related API was included in
GTK+ 3.4 to provide [basic touch
support](http://blogs.gnome.org/mclasen/2012/03/02/gtk-hackfest-aftermath/).
Carlos Garnacho on his blog has demonstration code, the
[gtkgesturesinterpreter.c](http://git.gnome.org/browse/gtk+/tree/gtk/gtkgesturesinterpreter.c?h=multitouch#n1332)
along with a demo video.

#### Fedora

Already much of the [multitouch
plumbing](http://fedoraproject.org/wiki/Features/Multitouch_support) is
in place. Fedora 19 is scheduled for May 21,2013 and will have Gnome
3.8.

### KDE Desktop

"Qt has had multitouch for quite some time and KDE also makes use of it
already on X11. E.g. Plasma Active on Mer supports multi touch, but
Plasma Active on openSUSE does not."

#### Qt

Qt5.0 uses a different method to define the beginning and end of a touch
"gesture" compared to XI 2.2. Qt's current method will be retained and
perhaps enhanced.

#### openSUSE

?

### Unity Desktop

Ubuntu has devoted considerable effort into implementing multitouch
support for users. A good overview is provided by the Ubuntu wiki's
[Multitouch page](https://wiki.ubuntu.com/Multitouch/).

#### Unity System Gestures

**Unity multitouch gestures with the Wacom driver:** Disabling the
xf86-input-wacom Gesture parameter makes available the 3 and 4 finger
Unity system gestures. The tradeoff is you lose 2FGT gestures such as
right click, scroll, and pinch zoom. Unfortunately it does not appear
Touchégg can be used to add back the 2FGT gestures. It may be Unity
consumes 2FG touches although it does not currently have 2FG system
gestures. Because Ginn is seemingly not compatible with the Unity
Desktop in Precise or later it isn't available to investigate 2FGT
further. Given the lack of two finger touch availability the only
practical option for 1 through 4 finger touch is to place touch on the
Synaptic driver.

**Unity multitouch gestures with the Synaptics driver:** The
xf86-input-synaptics driver also includes 2FGT gesture support. Three
and 4 finger Unity system gestures are available automatically. The
following assumes you have your Wacom tablet attached to a Desktop
computer that does not have a Synaptic touchpad.

To use the Synaptics driver first confirm you have the xorg.conf.d
directory in /etc/X11. If not create it:

    sudo mkdir /etc/X11/xorg.conf.d

Then create the custom file 54-WacomMT-synaptics.conf:

    gksudo gedit /etc/X11/xorg.conf.d/54-WacomMT-synaptics.conf

and paste the following snippet into it.

    Section "InputClass"
            Identifier "Wacom MT on synaptics"
            MatchIsTouchpad "on"
            MatchProduct "Finger"
            MatchDevicePath "/dev/input/event*"
            Driver "synaptics"
    EndSection

This has the severe disadvantage of breaking Buttons/ExpressKeys
configuration by xsetwacom because the Buttons are now handled by
xf86-input-synaptics. So the Synaptics driver is not a tenable option
for the Intuos5 or Cintiq 24HD touch tablets. And the BambooPT is a
judgement call.

For one and two finger touch open System Settings and open Mouse and
Touchpad. Go to the Touchpad tab, which now should be present. Under
General uncheck "Disable touchpad while typing" and make sure "Enable
mouse clicks with touchpad" is checked. Under Scrolling select
"Two-finger scrolling" and "Enable horizontal scrolling". Under Pointer
Speed set Acceleration and Sensitivity to your preferences.

The following is a list of touch Gestures with xf86-input-synaptics in
Unity.

-   1 finger tap &gt; left click
-   2 finger dbl. tap &gt; right click
-   2 fingers horizontal up or down &gt; vertical scroll
-   2 fingers vertical left or right &gt; horizontal scroll
-   3 finger press and drag &gt; move window

  
(3 finger tap shows grab handles)

-   4 finger swipe left/right &gt; reveal/hide launcher (if the dock
    autohide is enabled)
-   4 finger tap &gt; open or close Dash

BambooPT button functions in xf86-input-synaptics as set by the kernel
driver wacom.ko.

-   Button 1 left click
-   Button 2 up
-   Button 3 down
-   Button 4 right click

Note that one and two finger touch is being processed in the synaptics
driver and so a X Server multitouch aware application would not see the
one and two finger touches. In that case you have to disable "Enable
mouse clicks with touchpad" and either disable scrolling or select Edge
scrolling in Mouse and Touchpad for the application to see the X Server
one and two finger touches. This is explained on the wiki page [Touchpad
Support](https://wiki.ubuntu.com/Multitouch/TouchpadSupport).

The Synaptics driver has a many configuration options. See **man
synaptics** and **man synclient** in a terminal.

#### Gesture Engines for GNOME Shell, KDE, MATE, and Cinnamon Desktops

In addition to the one built into the Unity Desktop Ubuntu has two
gesture engines. Ginn was the original one and is being replaced by
Touchégg. If you don't use the Unity Desktop you'll need Ginn or
Touchégg for 2, 3, and 4 finger gestures. Ginn is installed by default
through Precise. However Ginn appears incompatible with the version of
the Unity Desktop in Precise and later. See the launchpad bug report
[\#985121](https://bugs.launchpad.net/ubuntu/+source/ginn/+bug/985121).
Because Ginn is no longer installed by default after Precise and has
been moved to the Universe repository
[Touchégg](http://code.google.com/p/touchegg/) is probably the way to go
for Quantal and later. Especially since there is now a gesture
configuration gui for it. The Touchégg in Precise's repository is broken
and a newer version needs to be compiled to get it working in Precise, a
possible reason to stick with Ginn for Precise (without the Unity
Desktop) and earlier.

##### Ginn

Ginn is an acronym for Gesture Injector: No-GEIS, No-Toolkits. Available
at this [launchpad site](https://launchpad.net/ginn). Instructions for
editing the ginn configuration file wishes.xml (located at
/etc/ginn/wishes.xml) to add custom gestures is available in **man
ginn** entered in a terminal. Also see the [ginn wiki
page](https://wiki.ubuntu.com/Multitouch/Ginn). There is a duplicate
wishes.xml file in /usr/share located at /usr/share/ginn.

Ginn, once installed from the repository, works in Kubuntu Precise
(12.04) with xf86-input-wacom-0.18.0 or higher as soon as the xsetwacom
command **Gesture off** is run. Presumably it will also work with the
Gnome Shell Desktop and Mint 13 Maya's Cinnamon or MATE Desktops.

##### Touchégg

Downloads available at the [touchegg
site](http://code.google.com/p/touchegg/) along with a wiki with
instructions. ["OS X-like multitouch gestures for Macbook Pro running
Ubuntu
12.10"](http://task3.cc/1068/os-x-like-multitouch-gestures-for-macbook-pro-running-ubuntu-12-10/)
appears to be a good tutorial.

The instructions on the site to compile touchegg did not work. Using
**sudo apt-get build-dep touchegg** installed **libgeis-dev libqt4-dev
libqt4-qt3support qt4-linguist-tools qt4-qmake**. An error is generated
on make which required installing **libutouch-geis-dev**. Touchégg then
compiles and installs. However the dependencies pulled in by build-dep
did not overlap much with the instructions alternate **sudo apt-get
install build-essential libqt4-dev utouch libx11-6 libxtst-dev**. The
touchegg-1.1 CHANGELOG says:

    [+] Touchégg v1.1 (2012/07/22)
     - Added Ubuntu Precise compatibility

So to compile touchegg in Maya/Precise the following should work.
Download Touchégg v1.1 onto your Desktop and extract it. Then:

    sudo apt-get install build-essential libqt4-dev utouch libx11-6 libxtst-dev libgeis-dev libqt4-qt3support qt4-linguist-tools qt4-qmake libutouch-geis-dev
    cd Desktop/touchegg-1.1
    qmake
    make
    sudo make install

The touchegg.conf file appears at ~/.config/touchegg after a reboot.
Like ginn there is a duplicate example .conf file in /usr/share at
/usr/share/touchegg. The Touchegg-gce gui edits the .conf file at
~/.config/touchegg/touchegg.conf and presumably that is the active one.
To install the gui on Maya/Precise I did the following.

    cd Desktop
    git clone https://github.com/Raffarti/Touchegg-gce.git
    cd Touchegg-gce
    qmake
    make

Didn't need any new dependencies beyond what were already installed for
Touchégg. There is no install so run the touchegg-gce binary in the
Touchegg-gce folder. Can use a launcher if you want to use the gui.

Touchégg, once compiled and installed, works in Kubuntu Precise (12.04)
with xf86-input-wacom-0.18.0 or higher as soon as the xsetwacom command
**Gesture off** is run. Presumably it will also work in Gnome Shell and
Mint 13 Maya with Cinnamon and MATE.

#### Turning Unity Gestures Off

For UI consistency reasons Unity makes no provision for modifying its
stock gestures or adding gestures. Also by design Unity does not allow
disabling its system gestures which is necessary to use another gesture
engine to customize gestures. See ["Unity Gesture UI
Guidelines"](https://docs.google.com/document/d/1WJF8YdphrorvHiB5FFVxsitL5Pz-tpZ5-kmtzoD3tFc/edit#).
On askubuntu ["How can I disable arbitrary default multitouch gestures
in
Unity?"](http://askubuntu.com/questions/57586/how-can-i-disable-arbitrary-default-multitouch-gestures-in-unity)
is a good discussion of what's needed. The gesture code has to be
disabled and then Unity re-compiled.
