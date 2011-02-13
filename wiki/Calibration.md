---
title: Calibration
permalink: wiki/Calibration/
layout: wiki
tags:
 - HOWTO
---

The default coordinate settings for your tablet or tablet pc that are
supplied by [xf86-input-wacom](xf86-input-wacom "wikilink") are usually
accurate. However depending on your hardware or setup you may need to do
some fine tuning. The default coordinates are available in the
Xorg.0.log file in /var/log and would look something like this.

` Wacom BambooFun 2FG 4x5 Pen stylus: top X=0 top Y=0 bottom X=14720 bottom Y=9200`

If you have a serial or usb tablet pc it will also tell you what port or
device input event the data is coming in on. Usually tty/S0 for serial
tablet pc's.

Find Your Actual Coordinates
----------------------------

There are a few different ways to accomplish this.

### Calibration Programs

Several calibration programs are available. One such you can use is
[xinput\_calibrator](/wiki/External_applications#xinput_calibrator "wikilink").
To install it a tarball, several packages for different distributions,
and a Ubuntu PPA are available. Installing from the source code is
simple and described in the README, although you should probably use the
*--prefix=/usr* flag, e.g.

` ./autogen.sh --prefix=/usr`

before doing your *make && make install*. Entering in a console:

` xinput_calibrator --list`

will return a list of devices xinput\_calibrator thinks it can
calibrate. Using either the ID \# or "device name" (in quotes) returned
proceed to calibrate your device. Four cross hairs will appear in
sequence and on finishing xinput-calibrator will return output similar
to the following.

    ~$ xinput_calibrator --device "9"
    Calibrating standard Xorg driver "Wacom BambooFun 2FG 4x5 Pen stylus"
        current calibration values: min_x=0, max_x=14720 and min_y=0, max_y=9200
        If these values are estimated wrong, either supply it manually with the --precalib option, or run the 'get_precalib.sh' script to automatically get it (through HAL).

    --> Making the calibration permanent <--
      copy the policy below into '/etc/hal/fdi/policy/touchscreen.fdi'
    <match key="info.product" contains="!!Name_Of_TouchScreen!!">
      <merge key="input.x11_options.minx" type="string">9</merge>
      <merge key="input.x11_options.maxx" type="string">14740</merge>
      <merge key="input.x11_options.miny" type="string">33</merge>
      <merge key="input.x11_options.maxy" type="string">9215</merge>
    </match>

    Change '!!Name_Of_TouchScreen!!' to your device's name in the config above.

You can see the default values but the 4 values you are interested in
are under the *Making the calibration permanent* heading. So minx =
TopX, maxx = BottomX, miny = TopY, and maxy = BottomY. You'll want to
repeat the calibration several times and use the smallest and largest
values or perhaps an average if you see no trend. Then use the TopX & Y
and BottomX & Y coordinate values in [Apply the
Coordinates](/wiki/Calibration#Apply_the_Coordinates "wikilink") below.

### xinput test

If you have a usb tablet a convenient way to read your default values
from Xorg.0.log is to first unplug your tablet's usb cable. Then enter
in a terminal:

` tail -f /var/log/Xorg.0.log`

and hotplug your tablet after you run the command. This will also give
you your "device name" and event number as the sample output below
shows. For calibration you only need the stylus and touch "device name",
e.g.

    [  5020.783] (**) Option "Device" "/dev/input/event5"
    [  5021.221] (--) Wacom BambooFun 2FG 4x5 Pen stylus: top X=0 top Y=0 bottom X=14720 bottom Y=9200 resol X=100000 resol Y=100000

Once you have determined the device name ('xinput list' is an option
also) you are ready to use xinput test. Enter into a console:

    xinput test "device name"
    or
    xinput test -proximity "device name"

e.g.

` xinput test "Wacom BambooFun 2FG 4x5 Pen stylus"`

Using the *-proximity* switch will tell you when you've gone beyond the
tablets edge. Then trace along the entire periphery of the tablet. This
is easier if you have a bezel lip like tablet pc's do. When you have
finished make sure the terminal is in focus and enter ctrl-z to shut
*xinput test* down.

You'll see 'motion' followed by 6 numbers. Since there is a continuous
stream of motion events you end up with 6 columns of numbers. The first
column is X and the second Y. Scan down the X column and write down the
smallest and largest X value. Do the same with the Y column, writing
down the smallest and largest Y value. The smallest X and Y are your
TopX & Y and your largest are your BottomX & Y. For example with a
Bamboo P & T the results were 1,1 and 14712,9195 which is pretty close
to the default 0,0 14720,9200 in Xorg.0.log.

Using the *-proximity* switch can refine your values a little more,
although because the columns do not line up it is a little harder to
read. The results were 1,0 and 14720,9197 which are almost spot on the
default 0,0 14720,9200. The conclusion is the tested Bamboo P & T tablet
is fine with the default settings.

This method is a little tedious but not too bad all in all.

While you could also use *evtest* (event test) if you specify the
device's event number,

` sudo evtest /dev/input/event5`

to obtain the coordinate values, in practice it is not that useful.
Evtest only gives you the coordinates of your first touch of the screen
immediately after you run the command. So you have to constantly shut it
off with ctrl-z and restart it and touch the screen again and again with
your stylus or finger, trying to zero in on the minumum and maximum
coordinates.

### Guestimate the Coordinate Values

If you have some experience you may want to eyeball the situation and
plug in your "educated" guesses. Probably not the best way to go as it
usually requires some trial and error.

Apply the Coordinates
---------------------

Now that you have acquired your TopX & Y and BottomX & Y values you can
implement them with [xsetwacom](xsetwacom "wikilink") commands. Note the
eraser and cursor coordinates are the same as the stylus coordinates.
For example the default values of a Cintiq look like the following.

    xsetwacom set "Wacom Cintiq 21UX2 stylus" topx "0"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" topy "0"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomx "87200"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomy "65600"

But if calibration showed different coordinate values then substitute
them in instead.

    xsetwacom set "Wacom Cintiq 21UX2 stylus" topx "-20"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" topy "12"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomx "87064"
    xsetwacom set "Wacom Cintiq 21UX2 stylus" bottomy "65691"

Notice negative TopX & Y values are allowed. You'll need add these
commands to a startup script as they will not survive a reboot. And use
the appropriate "device name" for each device you want to apply the
coordinate values to.

Of course you can also apply the coordinates statically to the
wacom.conf in
[xorg.conf.d](/wiki/Configuring_X#Hotplugging_setup_with_udev "wikilink") or
to the
[xorg.conf](/wiki/Configuring_X#Manual_setup_in_the_xorg.conf "wikilink")
using:

    Option "TopX" "-20"
    Option "TopY" "12"
    Option "BottomX" "87064"
    Option "BottomY" "65691"

In the xorg.conf you would add the coordinates to each section required,
such as stylus, eraser, and cursor. And for the wacom.conf in the
appropriate usb or serial snippet below the *Driver "wacom"* line. Touch
will have a different set of coordinates.