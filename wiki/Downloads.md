---
title: Downloads
permalink: wiki/Downloads/
layout: wiki
---

Many distributions come with our driver pre-installed. Oftentimes, you
can simply plug in your tablet and start working. However, in some cases
a model may be too new (or obscure) to be supported out of the box. It
may also be that your system is missing the necessary drivers for some
reason. In these cases, our three packages below may help. Each serves a
different purpose and not all are necessary. Please click on the links
to get information on how to install each. Our current drivers are
compatible with most distributions released since 2010.

**Before proceeding, please use your distribution's package manager to
perform a system update.** This will ensure the latest drivers and
utilities are installed and in some cases may be sufficient on its own
to get a new tablet working.

| Kernel Driver                                                                                                                                                                                                                                                                             | X Driver                                                                                                                                                                                                                                                                                                  | libwacom                                                                                                                                                                                                                                        |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A kernel driver is necessary to initalize the tablet and translate hardware-specific protocols into standard input events. We contribute patches directly to the Linux kernel, as well as maintain an out-of-tree kernel module that enables old kernels to work with many newer tablets. | An X driver is responsible for translating kernel events into XInput events that can be used by applications such as GIMP. We provide an X driver that virtualizes the tablet into logical devices (e.g. stylus, eraser, touch) which report extended data like pressure and high-resolution coordinates. | The **libwacom** library provides applications with a way to get information about any tablets that are currently connected. It may be used by control panels to better support devices and provide only relevant options or defaults for each. |
| *Install **[input-wacom](input-wacom "wikilink")** if your tablet is not recognized as an input device by the system.*                                                                                                                                                                    | *Install **[xf86-input-wacom](xf86-input-wacom "wikilink")** if your tablet is recognized but missing functionality.*                                                                                                                                                                                     | *Install **[libwacom](libwacom "wikilink")** if your tablet is not recognized by the control panel.*                                                                                                                                            |

The following links may be of interest to some users. Please be aware
that some links may be to projects which are neither developed nor
supported by the Linux Wacom Project.

-   [linuxwacom](linuxwacom "wikilink"): Legacy all-in-one driver for
    systems using Linux &lt; 2.6.30 or xorg-server &lt; 1.7
-   [wdaemon](wdaemon "wikilink"): Linux Wacom Hotplug Daemon
-   [wacomtablet](wacomtablet "wikilink"): Solaris Driver
-   [Wacom Control
    Panel](http://gtk-apps.org/content/show.php/Wacom+Control+Panel?content=104309)
    (GNOME 2.x only, GNOME 3 has a wacom-specific control panel applet)
