---
title: Analysing kernel events
permalink: wiki/Analysing_kernel_events/
layout: wiki
tags:
 - DeveloperPages
---

This page describes how to view and analyze kernel events.

The **evemu-record** tool can be used to capture input events from the
kernel and display them in a human-readable form. Its often provided as
part of a distribution's "evemu" or "evemu-tools" package, and is also
available for compiling from source
[here](http://cgit.freedesktop.org/evtest/). This tool takes the place
of **evtest**, which is no longer maintained (but might be the only
option for older distributions).

Installing
==========

| Fedora, Red Hat, Mageia       | `$ sudo -s 'yum makecache && yum install evemu'`            |
|-------------------------------|-------------------------------------------------------------|
| Ubuntu, Mint, Debian          | `$ sudo -s 'apt-get update && apt-get install evemu-tools'` |
| SUSE                          | `$ sudo -s 'zypper update && zypper install evemu'`         |
| Arch Linux, Antergos, Manjaro | `$ sudo -s 'pacman -Sy && pacman -S evemu'`                 |
| Gentoo                        | `$ sudo -s 'emerge --sync && emerge evemu'`                 |

Capturing Events
================

To capture events with **evemu-record**, simply run the command as root
or with 'sudo', and then choose one of the listed devices. Some
preliminary device information will be printed; bring the tool into
proximity and events should be printed out in real-time. If multiple
devices are listed (e.g. "Graphire4 4x5" and "Graphire4 4x5 Pad"), you
may need to experiment to see which device generates the events your
interested in. If you need to capture from multiple devices
simultaneously, simply run the command a second time in a different
window.

If you are using the older **evtest** command, a list of devices will
not be printed out for you to choose from. Instead, you will need to
provide an appropriate device node on the command line. If you are
unsure what device to use, you can examine the contents of
*/proc/bus/input/devices*. The "Handlers" line for each listed device
defines what device node should be used (e.g. Handlers="event14 mouse3"
indicates that you should use */dev/input/event14*).

**Note: If you are running xf86-input-wacom 0.17.0 or earlier, neither
evemu-record nor evtest will be able to run while the X driver is
running. See "[Disabling the X
driver](#Disabling_the_X_driver "wikilink")" below if necessary.**

Analyzing Output
================

The output below is shortened for brevity.

    # EVEMU 1.2
    # Input device name: "Wacom Cintiq 24HD touch Pen"
    # Input device ID: bus 0x03 vendor 0x56a product 0xf8 version 0x110
    # Supported events:

    [...]

    #   Event type 1 (EV_KEY)
    #     Event code 320 (BTN_TOOL_PEN)
    #     Event code 321 (BTN_TOOL_RUBBER)
    #     Event code 322 (BTN_TOOL_BRUSH)
    #     Event code 323 (BTN_TOOL_PENCIL)
    #     Event code 324 (BTN_TOOL_AIRBRUSH)
    #     Event code 330 (BTN_TOUCH)
    #     Event code 331 (BTN_STYLUS)
    #     Event code 332 (BTN_STYLUS2)
    #   Event type 3 (EV_ABS)
    #     Event code 0 (ABS_X)
    #       Value      0
    #       Min      200
    #       Max   104280
    #       Fuzz       4
    #       Flat       0
    #       Resolution 200
    #     Event code 1 (ABS_Y)
    #       Value      0
    #       Min      200
    #       Max    65400
    #       Fuzz       4
    #       Flat       0
    #       Resolution 200

    [...]

The output header contains device information such as name, IDs, and
events supported by the device. For example, input the output above we
can see that the tablet supports multiple tool types (pen, rubber,
brush, etc.), has a button to detect when its touching the screen, and
two buttons on the stylus. It also has absolute X and Y axes (plus more,
the output was shortened). The X driver uses this information to set up
the device correctly.

Below the header will appear a stream of events from the kernel. To see
events, you'll need to tap the tablet with the pen or other tool. An
example event stream looks like the following:

    ################################
    #      Waiting for events      #
    ################################
    E: 0.000000 0003 0000 85450     # EV_ABS / ABS_X                85450
    E: 0.000000 0003 0001 38661     # EV_ABS / ABS_Y                38661
    E: 0.000000 0003 0019 0036      # EV_ABS / ABS_DISTANCE         36
    E: 0.000000 0003 001a 0072      # EV_ABS / ABS_TILT_X           72
    E: 0.000000 0003 001b 0059      # EV_ABS / ABS_TILT_Y           59
    E: 0.000000 0003 0028 1443842   # EV_ABS / ABS_MISC             1443842
    E: 0.000000 0001 0140 0001      # EV_KEY / BTN_TOOL_PEN         1
    E: 0.000000 0004 0000 914359898 # EV_MSC / MSC_SERIAL           914359898
    E: 0.000000 0000 0000 0000      # ------------ SYN_REPORT (0) ----------
    E: 0.006905 0003 0000 85429     # EV_ABS / ABS_X                85429
    E: 0.006905 0003 0001 38829     # EV_ABS / ABS_Y                38829
    E: 0.006905 0003 0019 0035      # EV_ABS / ABS_DISTANCE         35
    E: 0.006905 0003 001a 0071      # EV_ABS / ABS_TILT_X           71
    E: 0.006905 0004 0000 914359898 # EV_MSC / MSC_SERIAL           914359898
    E: 0.006905 0000 0000 0000      # ------------ SYN_REPORT (0) ----------
    E: 0.014999 0003 0000 85281     # EV_ABS / ABS_X                85281
    E: 0.014999 0003 0001 39044     # EV_ABS / ABS_Y                39044
    E: 0.014999 0003 0019 0034      # EV_ABS / ABS_DISTANCE         34
    E: 0.014999 0003 001b 0058      # EV_ABS / ABS_TILT_Y           58
    E: 0.014999 0004 0000 914359898 # EV_MSC / MSC_SERIAL           914359898
    E: 0.014999 0000 0000 0000      # ------------ SYN_REPORT (0) ----------
    E: 0.022000 0003 0000 85070     # EV_ABS / ABS_X                85070
    E: 0.022000 0003 0001 39368     # EV_ABS / ABS_Y                39368
    E: 0.022000 0003 0019 0032      # EV_ABS / ABS_DISTANCE         32
    E: 0.022000 0003 001b 0057      # EV_ABS / ABS_TILT_Y           57
    E: 0.022000 0004 0000 914359898 # EV_MSC / MSC_SERIAL           914359898
    E: 0.022000 0000 0000 0000      # ------------ SYN_REPORT (0) ----------

    [...]

*SYN\_REPORT* divides the different hardware events. i.e in the above
output, you can see three event frames. The events themselves update
various axes (x, y, distance, etc.). The output itself is mostly
self-explanatory.

If you don't see any events, the most common cause is that you forgot to
[disable the X driver](#Disabling_the_X_driver "wikilink"). If you are
sure you have done so, the kernel driver does not work with your model.
You need to [submit a bug](/wiki/Submitting_Bugs "wikilink").

Disabling the X driver
======================

Older versions of the X driver (xf86-input-wacom 0.17 and earlier) tell
the kernel that it would like to "grab" the device file for exclusive
access. Once the grab is established, only a single process (the X
server) will receive events from that device. Thus, if you run
evemu-record or evtest from within X, you will see device information,
but no events will be printed out.

There are several ways to get around this:

-   VT switch away by pressing Ctrl+Alt+F1. This puts you on a TTY where
    you can log in and run evtest from. To get back to X, press
    Ctrl+Alt+F7. Note that distributions use TTYs differently, so you
    may have to cycle through the various Fx keys to find the right
    ones. This is the recommended method. It is fast and does not
    destroy your session.
-   Disable the device in X by running *xinput set-prop "device name"
    "Device Enabled" 0*. Note that you need to do this for all tools
    (stylus, eraser, etc.).
-   Stop X. Usually this can be achieved by switching into runlevel 3
    with *sudo init 3*. Note that this will close your session.
