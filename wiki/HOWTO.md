---
title: HOWTO
permalink: wiki/HOWTO/
layout: wiki
---

Introduction
============

This document began in November 2002 as a HOWTO for setting up a USB
Wacom Intuos2 on Redhat 8.0. It has since grown to cover all Wacom
tablets, USB and serial, running on various different Linux
distributions. As of December 2002, this project has transformed into
the Linux Wacom Project. Work on this document is on-going so if you
find an error, have a question, or have something to add, please send an
email to: linuxwacom-discuss@lists.sf.net.

How To Use This Document
------------------------

Where the guide differs between distributions or packages, I have added
a comment which appears indented with a light blue background.

Also, it bears mentioning since it's a detail often missed: there are
two drivers in the Linux Wacom Project- wacom.o and wacom\_drv.o. The
first driver is the USB kernel driver. The second driver is the XFree86
Wacom driver. Serial users need only be concerned with the wacom\_drv.o
driver. USB users need both. If you try to use the wacom\_drv.o driver
in place of the wacom.o kernel driver or visa-versa, things generally
won't work.

Next, this document was written with the assumption that you are
starting from scratch with a relatively recent distribution of the Linux
kernel. Also, if you have already added lines to your XFree86/Xorg
configuration file (XF86Config/XF86Config-4 or xorg.conf), you should
comment them out and restart X. Since we'll be stepping through the
entire process, we need X to ignore the tablet until we're ready.
Otherwise, X will just get in the way.

Finally, if you know what you're doing, you can leave your X settings
intact, print this out, switch to runlevel 3, and follow along from the
console. An HTML version of this document can be found in the docs
directory at docs.html.

Wacom Driver Theory of Operation
================================

Wacom tablets are available in serial and USB configurations. They are
also sold as embedded products in certain tablet PC's. I will cover all
three types, but the serial case is the most straightforward, so we'll
start there.

### Serial Tablet Operation - The Short Story

When a Wacom serial tablet is connected to a COM port, software can
interact with it directly by opening the appropriate device, usually
/dev/ttyS0. The XFree86 Wacom driver (wacom\_drv.o) does precisely this,
and all stylus movements are converted into XInput events for programs
like the Gimp to turn into fluid brush strokes.

Wacom tablets are capable of handling a number of different data
protocols, and the Linux Wacom Project code currently utilitizes two,
Wacom Protocol IV and Protocol V. Each operates with a fixed-sized
packet, the length of which depends on the model and ROM version.

When the serial tablet is reset, it defaults to a standard baud rate,
often 9600 baud. From there, the device type can be queried, and if the
tablet supports it, the baud rate increased to a higher value.
Additionally, model parameters like tablet size can be queried directly
to determine which features are available.

Once configured, the tablet streams data back to the application as
tools are brought into and out of proximity, are pressed against the
tablet surface, or are tilted or inverted.

### USB Tablet Operation - The Long Story

Initially at least, the USB Wacom tablet is an HID compliant device, and
when first connected to the computer, will identify itself as such.
Unfortunately, this is not what you want because in this mode, you will
not get any of the fancy features. The hid-core.c, mousedev.c, and
usbmouse.c kernel drivers contain exceptions for the wacom; when the
device is detected, they ignore the tablet. In this way, the more
sophisticated wacom driver has the opportunity to assume control.

The first thing that the driver does is register itself with the USB
subsystem and wait for work to do. When the user plugs the device in, or
the device is first detected, the USB subsystem shops the vendor and
device identifier around, checking it against different drivers. The
wacom driver takes responsibility for the tablet and then notifies the
event system that it will be providing data. It then asks the tablet to
switch from HID-compliant mode to "mode 2", a wacom-specific protocol
which allows for values like pressure, Z rotation, and tilt. As
information arrives, the wacom driver dutifully converts the data into
real-world values and hands it on to the event system.

From here, any usermode application can get access to the event data by
opening /dev/input/event0. A stream of events including mouse movements,
clicks, and proximity updates can be read from the device. Similar to
the serial case, XFree86's Wacom driver (wacom\_drv.o) has the ability
to read this device, and performs filtering on the data before convert
the Linux input events into XInput events.

By breaking the responsibility for the data into three distinct levels,
the kernel code remains simple and robust, the applications generalized,
and the fancy features commonly accessible to all GUI applications in
the X window system itself. This document walks down the entire data
path from the USB kernel driver to the gimp application.

### Embedded Device Operation - Tablet PC with Wacom Digitizer

Refer to Tablet PC page for detail.

Getting It Together
===================

This section is devoted to preparing your system for the installation.
Every distribution handles kernel modules and file locations a bit
differently, so the goal here is to return everything to a known state.

USB users will need to pay specific attention to discussions related to
kernel drivers and modules. Serial tablet users have it much easier, and
can bypass this. Both users will need to make changes to the
XFree86/Xorg configuration file.

Before We Start
---------------

From the beginning, let's make certain that we are on the same page.
First, if you have Wacom related lines in your XF86Config/XF86Config-4
or xorg.conf files already, you should comment them out or remove them.
In particular, this includes InputDevice sections with a driver set to
"wacom" and their corresponding InputDevice lines in ServerLayout. When
that's done, restart X.

It would be a wise idea at this time to check your XFree86 or Xorg log
file for references to wacom, wcm, or tablet. If X persists in trying to
interact with the tablet, things will only be problematic later. The log
file is often found at /var/log/XFree86.0.log or /var/log/Xorg.0.log.

To be prepared for the coming steps, it would be nice if you know what X
server and Linux kernel versions are running on your system. The X
-version command reveals those information for you.

If you have a USB tablet, you need to check Wacom kernel drvier first
The USB Kernel Driver. Serial tablet or Tablet PC users can go directly
to the next section Downloading the Code page.

Downloading the Code
--------------------

The file linuxwacom-0.8.6-1.tar.bz2 is the stable package and contains
files that you will need to get your serial or USB tablet working. The
current beta package linuxwacom-0.8.5-12.tar.bz2 is also available and
may be used by people who are willing to help test new features. I will
never put a beta package on this site that I am not running myself on my
primary development machine. So you can be certain that if there are any
obvious show stoppers, they will be fixed before you get to see them.

Unpacking the tarball is usually a one-step process, but I show both
steps in case the typical -jxf option doesn't work with tar.

        [jej@ayukawa jej]$ bunzip2 linuxwacom-0.8.6-1.tar.bz2
        [jej@ayukawa jej]$ tar -xf linuxwacom-0.8.6-1.tar
        [jej@ayukawa jej]$ cd linuxwacom-0.8.6-1

Once in the package directory, you need only to configure and build the
code. This is described in more detail as you continue. The executables
and wacom\_drv.o are installed automatically; the kernel drivers have
different installation procedures depend on the kernel source you use.

If you are interested, the following tables contain the package contents
and release dates. Otherwise, let's continue. Stable files included for
linuxwacom-0.8.6-1:

| File                        | Comment                                                                                                              |
|-----------------------------|----------------------------------------------------------------------------------------------------------------------|
| configure                   | configure script for distribution independent builds                                                                 |
| prebuilt/install            | installer for the executables and Wacom X driver to a system identical to the development system.                    |
| prebuilt/uninstall          | unistaller for the executables                                                                                       |
| prebuilt/wacom.4x.gz        | man page for Wacom driver                                                                                            |
| prebuilt/32                 | Wacom X driver and its utility programs for XFree86 and X11R6/Xorg on x86 systems                                    |
| prebuilt/64                 | Wacom X driver and its utility programs for XFree86 and X11R6/Xorg on x86-64 systems                                 |
| src/util/wacdump.c          | a simple program for displaying tablet event data directly using ncurses                                             |
| src/util/xidump.c           | a diagnostic program for displaying XInput event data                                                                |
| src/util/wacscrn.c          | curses library for wacdump                                                                                           |
| src/util/wactablet.c        | wacom tablet library for wacdump                                                                                     |
| src/util/wacusb.c           | wacom USB protocol library for wacdump                                                                               |
| src/util/wacserial.c        | wacom serial protocol library for wacdump                                                                            |
| src/util/wactablet.h        | wacom tablet library for wacdump                                                                                     |
| src/util/wacusb.h           | wacom USB protocol library for wacdump                                                                               |
| src/util/wacserial.h        | wacom serial protocol library for wacdump                                                                            |
| src/util/xsetwacom.c        | a command line configuration tool for Wacom X driver                                                                 |
| src/util/wacomcfg.c         | configuration option library for xsetwacom                                                                           |
| src/util/wcmAction.c        | keystroke and modifier encoding/decoding                                                                             |
| src/util/wcmAction.h        | keystroke routine definitions                                                                                        |
| src/util/hal                | setup-wacom.c - a HAL service program for Wacom devices                                                              |
| src/util/10                 | linuxwacom.fdi - a HAL script for Wacom devices                                                                      |
| src/include/Xwacom.h        | configuration options for xsetwacom                                                                                  |
| src/2.6.x/wacom.c           | replacement kernel driver for kernel 2.6.x where x can be 9 and 10                                                   |
| src/2.6.x/wacom\_sys.c      | wacom kernel driver specific to each major kernel release, where x can be 11 to 28                                   |
| src/2.6.x/wacom\_wac.c      | wacom kernel driver processes tablet specific information, where x can be 16 or 28                                   |
| src/2.6.x/wacom.h           | wacom kernel driver header specific to each major kernel release, where x can be 11 to 28                            |
| src/2.6.16/wacom\_wac.h     | part of wacom kernel driver header for tablet specific declaration and definitions                                   |
| src/2.6.x/hid               | core.c - replacement kernel driver for kernel 2.6.x where x can be 8 to 16, use only if needed                       |
| src/xdrv/xf86Wacom.c        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Binary available in prebuilt directory. |
| src/xdrv/xf86Wacom.h        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile.                                         |
| src/xdrv/wcmCommon.c        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Common to USB and serial tablets        |
| src/xdrv/wcmXCommand.c      | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. APIs to xsetwacom.                      |
| src/xdrv/wcmCompat.c        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. XFree86 4.x support                     |
| src/xdrv/wcmConfig.c        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Configuration setup                     |
| src/xdrv/wcmFilter.c        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Raw data filters                        |
| src/xdrv/wcmFilter.h        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Raw data filters                        |
| src/xdrv/wcmISDV4.c         | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. General Tablet PC                       |
| src/xdrv/wcmSerial.c        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Serial tablet support                   |
| src/xdrv/wcmSerial.h        | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Serial tablet support                   |
| src/xdrv/wcmUSB.c           | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. USB tablet support                      |
| src/xdrv/wcmTilt2Rotation.c | source for wacom\_drv.o; requires XFree86/Xorg build environment to compile. Converting mouse tilt to rotation       |
| src/wacomxi/wacomcpl        | exec - a graphic configuration tool for wacom XFree86 driver                                                         |
| src/wacomxi/wacomxi.c       | calibration library for xsetwacom                                                                                    |
| src/wacomxi/wacomxi.h       | calibration library for xsetwacom                                                                                    |
| GPL                         | the GNU General Public License, in case you did not already have one lying around                                    |
|                             |                                                                                                                      |

### Stable Packages by Version and Date:

| File                              | Date       | Comment                                                                                                                                                                                                       |
|-----------------------------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| linuxwacom-0.8.6-1.tar.bz2        | 2010-04-09 | Support tablets 0xE2, 0xE3, and 0x9F. Support new serial ISDv4 Tablet PCs. Avoid duplicated and invalid devices and types for X server 1.4 and later. Support kernels up to 2.6.35. Add 5 new Bamboo devices. |
| linuxwacom-0.8.4-4.tar.bz2        | 2009-11-13 | Support: Intuos4; kernels up to 2.6.31; Xorg up to 1.6.0.                                                                                                                                                     |
| linuxwacom-0.8.2-2.tar.bz2        | 2009-01-19 | Support: USB Tablet PC with and without touch; kernels up to 2.6.28; Bamboo1 and Monarch; new wacomcpl features.                                                                                              |
| linuxwacom-0.8.0-3.tar.bz2        | 2008-05-23 | Support: kernels up to 2.6.25; Keystrokes for both buttons and expresskeys; New tablets: Bamboo series and Cintiq 12WX & 20WSX, and so much more that you can not afford to miss.                             |
| linuxwacom-0.7.8-3.tar.bz2        | 2007-08-15 | Supports new tablet, Bamboo. Provides prebuilt Wacom X driver and its utility programs for x86\_32 and x86\_64 systems. Adds many new xsetwacom options.                                                      |
| linuxwacom-0.7.6-4.tar.bz2        | 2006-12-01 | Updated xsetwacom and support kernel 2.6.17/18, Intuos3 4x6 and on-the-fly tablet rotation.                                                                                                                   |
| linuxwacom-0.7.4-3.tar.bz2        | 2006-06-19 | Supports kernels 2.6.15/16, 2 new Intuos3 (12x12 and 12x19), and DTF 521.                                                                                                                                     |
| linuxwacom-0.7.2.tar.bz2          | 2005-12-21 | Updated configuration script and support kernel 2.6.13/14, Graphire4, PL710, DTF720, Intuos3 6x11 and Volito2 .                                                                                               |
| linuxwacom-0.7.0-1.tar.bz2        | 2005-09-23 | Updated wacomcpl and support 16 buttons for all tools.                                                                                                                                                        |
| linuxwacom-0.7.0-x86-64-1.tar.bz2 | 2005-09-23 | Updated wacomcpl and support 16 buttons for all tools.                                                                                                                                                        |
| linuxwacom-0.6.8.tar.bz2          | 2005-05-05 | Support Cintiq 21UX and kernel 2.6.11.                                                                                                                                                                        |
| linuxwacom-0.6.6.tar.bz2          | 2004-12-01 | Build .ko locally and support kernel 2.6.10.                                                                                                                                                                  |
| linuxwacom-0.6.4.tar.bz2          | 2004-08-06 | Updated wacusb.c and fixed USB tablet protocol V dual input bug.                                                                                                                                              |
| linuxwacom-0.6.3.tar.bz2          | 2004-05-25 | Fixed tool on tablet and relative speed bugs.                                                                                                                                                                 |
| linuxwacom-0.6.2.tar.bz2          | 2004-04-02 | Fixed DoubleSpeed, DoubleRadius, and TwinView issues.                                                                                                                                                         |
| linuxwacom-0.6.1.tar.bz2          | 2004-03-02 | added wacomcpl, support kernel 2.4.24 and 2.6.2/3                                                                                                                                                             |
| linuxwacom-0.6.0.tar.bz2          | 2004-02-04 | added wacomcpl, support kernel 2.4.22 and 2.6.0                                                                                                                                                               |
| linuxwacom-0.4.1.tar.gz           | 2003-03-22 | added xidump, checks for ncurses                                                                                                                                                                              |
| linuxwacom-0.4.0.tar.gz           | 2003-01-31 |                                                                                                                                                                                                               |
|                                   |            |                                                                                                                                                                                                               |

### Beta Packages by Version and Date:

| File                            | Date       | Comment                                                                                                                                                                                                                                     |
|---------------------------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| linuxwacom-0.8.5-12.tar.bz2     | 2010-03-26 | Support tablets 0xE2, 0xE3, and 0x9F. Support new serial ISDv4 Tablet PCs. Avoid duplicated and invalid devices and types for X server 1.4 and later. Support kernels up to 2.6.34. Add 5 new Bamboo devices.                               |
| linuxwacom-0.8.3-6.tar.bz2      | 2009-06-28 | Support Inutos4 tablets. Support kernel upto 2.6.29. Support Xorg 1.6. Updated multi-monitor suppport                                                                                                                                       |
| linuxwacom-0.8.1-6.tar.bz2      | 2008-10-24 | Support USB Tablet PC with and without touch. Support kernel 2.6.27                                                                                                                                                                         |
| linuxwacom-0.7.9-11.tar.bz2     | 2008-04-11 | Support kernel 2.6.22 & 2.6.24. Temporary workaround for Xorg 7.3. Support Bamboo series and Cintiq 12WX & 20WSX. Updated wacomcpl for keystrokes. Support serial Tablet PC with touch.                                                     |
| linuxwacom-0.7.7-12.tar.bz2     | 2007-06-15 | Support Bamboo. Updated installer under prebuilt directory to install X driver as well as its associated utilities. Support non-overlapped multi-areas for same InputDevice. Support pad with button and keystroke events through xsetwacom |
| linuxwacom-0.7.5-4.tar.bz2      | 2006-09-29 | Support kernel 2.6.17/18, Intuos3 4x6, and tablet detach/attach as well as rotation while X running.                                                                                                                                        |
| linuxwacom-0.7.3-1.tar.bz2      | 2006-04-07 | Support kernel 2.6.15/16, Intuos3 12x12 and 12x19.                                                                                                                                                                                          |
| linuxwacom-0.7.1-2.tar.bz2      | 2005-12-06 | Support kernel 2.6.13/14, Graphire4, PL710, DTF720, Intuos3 6x11 and Volito2.                                                                                                                                                               |
| linuxwacom-0.6.9.tar.bz2        | 2005-08-08 | Support tablet orentation rotation.                                                                                                                                                                                                         |
| linuxwacom-0.6.9-x86-64.tar.bz2 | 2005-08-08 | Support x86-64 system.                                                                                                                                                                                                                      |
| linuxwacom-0.6.7.tar.bz2        | 2005-03-28 | Added Cintiq 21UX and kernel 2.6.11 support.                                                                                                                                                                                                |
| linuxwacom-0.6.5.tar.bz2        | 2004-11-05 | Added Intuos3 support.                                                                                                                                                                                                                      |
| linuxwacom-0.5.4-beta.tar.bz2   | 2003-12-22 | General Tablet PC support. 2.6.0 kernel support.                                                                                                                                                                                            |
| linuxwacom-0.5.3-beta.tar.gz    | 2003-11-12 | Added wacomcpl utility. 2.4.22 kernel support. TwinView support.                                                                                                                                                                            |
| linuxwacom-0.5.2-beta.tar.gz    | 2003-07-10 | Fixed Intuos filter code. 2.5 kernel support. Minor bug fixes.                                                                                                                                                                              |
| linuxwacom-0.5.1-beta.tar.gz    | 2003-06-15 | Completely refactored data path, configurability.                                                                                                                                                                                           |
| linuxwacom-0.5.0-beta.tar.gz    | 2003-02-12 | Updated PL code. Numerous tweaks.                                                                                                                                                                                                           |

The Root Account
----------------

If you are comfortable with the root account, paths, the /sbin
directory, and programs like modprobe, you can skip this section. This
is largely to clarify some things for people who are new to Linux and
get tripped up with the root account and paths. This is not meant to be
a tutorial, so if this is over your head, I would recommend reading a
book on Linux command line usage. All examples in this document assume
the bash shell.

Many of the procedures in this document need root access, and the
commands that are executed are located in places on the system that are
not normally accessed by typical users. In order to run the modprobe
command, for instance, you must have root access. Additionally, if the
/sbin directory which contains modprobe does not appear in your path,
you must specify the full pathname, /sbin/modprobe, to run the command.
Here is an example of the problem, followed by solutions.

        [jej@ayukawa jej]$ modprobe foo
        bash: modprobe: command not found
        [jej@ayukawa jej]$ locate modprobe
        /sbin/modprobe
        [jej@ayukawa jej]$ echo $PATH
        /bin:/usr/bin: ... :/home/jej/bin

Normal users do not have /sbin in their path, so running modprobe
directly fails. Running the program using the full pathname
(/sbin/modprobe) will solve this, as will adding /sbin to the path. But
there is another problem, as we will see:

        [jej@ayukawa jej]$ /sbin/modprobe foo
        foo.o: create_module: Operation not permitted

Normal users are not allowed to run this command. For that, we need to
be root. The su command stands for "substitute user" since it can be
used to become any user on the system, but it is generally known by the
incorrect but very memorable mnemonic "superuser."

        [jej@ayukawa jej]$ su
        Password:
        [root@ayukawa jej]# whoami
        root

Note the change to the root account, and the additional change from $ to
\# on the prompt. I maintain this convention in all the examples in this
document, so if you get an "access denied" error, check the prompt. You
probably need to be root.

Now that we have root access, is /sbin in our path? No. We have only
been granted the privileges of root; we are not really in the root
account's environment. Most notably, the home directory ($HOME) changes,
but the path ($PATH) stays the same. Thus, becoming root is not
sufficient to run modprobe without the full pathname, but does solve the
access problem.

        [root@ayukawa jej]# modprobe foo
        bash: modprobe: command not found
        [root@ayukawa jej]# export PATH=$PATH:/sbin
        [root@ayukawa jej]# modprobe foo
        [root@ayukawa jej]#

In this example, the user adds the /sbin directory to the path and can
run modprobe normally. export is a bash shell command that changes
aspects of your environment; in this case, /sbin is appended to the
path. In the highly unlikely event that you are using a different shell,
which for novice users seems unwise to me, you would need to use a
different command. Redhat, Mandrake, and similar distributions all use
bash by default, so it is unlikely that you would be using anything
else.

At any rate, changing the path is a reasonably good solution, if you can
remember the syntax of the export command.

Another approach to this problem is to do more than just "be root," but
to run in the root account's environment. This is accomplished with the
"su -" command and provides you with root's normal path, including the
/sbin directory. The unfortunate side-effect is that you wind up in
root's home directory, requiring you to cd back to the original
directory in which you were working.

        [jej@ayukawa src]$ pwd
        /home/jej/src/linuxwacom/src
        [jej@ayukawa src]$ su -
        [root@ayukawa root]# cd /home/jej/src/linuxwacom/src
        [root@ayukawa src]# echo $PATH
        /bin:/sbin:/usr/bin: ... :/root/bin

Here, the user starts in the package's src directory, but upon invoking
"su -" is magically shuttled off to root's home directory. A quick cd
back to the package directory and all is better. And, as demonstrated
above, the path conveniently contains /sbin.

So that leaves you with two immediate options, and one potential
long-term option:

Option One: Become root and add /sbin to the path.

        [jej@ayukawa src]$ su
        [jej@ayukawa src]# export PATH=$PATH:/sbin

Option Two: Become root using root's environment and cd back.

        [jej@ayukawa src]$ su -
        [root@ayukawa root]# cd /home/jej/src/linuxwacom/src

Option Three (recommended): Add /sbin to your personal account's path

        [jej@ayukawa src]$ export PATH=$PATH:/sbin
        [jej@ayukawa src]$ su
        [root@ayukawa src]# echo $PATH
        /bin:/usr/bin: ... :/home/jej/bin:/sbin

By adding the path early in the session, it becomes available every time
you su to root later on. You could also add the export command to the
.bash\_profile file in your home directory and have the path set
automatically when you log in.

To exit from the root account and return to your normal account, you can
use the exit command or type Ctrl-D on an empty line.

        [root@ayukawa src]# exit
        [jej@ayukawa src]$

If any of this is not explained clearly, drop me a line and let me know
where you got stuck. I'd be happy to clarify directly and update this
page for future readers.

Install from Prebuilt
---------------------

We have created 2 sets of prebuilt Wacom X driver and its utility
programs under linuxwacom-0.8.6-1/prebuilt; one for x86-32 systems, the
other for x86-64 systems.

If you don't plan to change anything in the driver, following steps will
install the prebuilt files for you:

Note: Please remove the existing linuxwacom package if your distro has
installed one for you. Use the command, such as yum remove or apt-get
uninstall, provided by your distro. Please DO NOT remove the driver
files manually by rm.

        [jej@ayukawa jej]$ cd linuxwacom-0.8.6-1/prebuilt
        [jej@ayukawa prebuilt]$ su
        [jej@ayukawa prebuilt]# ./uninstall
        [jej@ayukawa prebuilt]# ./install

Serial tablet (most Tablet PCs are serial) users can skip the rest
sections and go to Viewing Wacom Data (wacdump) page for details on
viewing the tablet output now.

If you use an older USB tablet and your running kernel was released 6
months later than your tablet was first seen in market, you most
probably can skip next section of this chapter and the whole chapter 4
too. If you would like to make certain, continue to next section.

Note:If the prebuilt driver doesn't work for you, you would probably end
up building your own driver in the following sections. Kory Prince has
provided a Python script, getwacom.py, to download, compile, and install
the latest linuxwacom release. Please try Kory's script if you like to
take a shortcut to install the driver.

Configuring the Package
-----------------------

This section describes how to configure the package. You can run the
configure script now as the samples below demonstrate, or later when you
reach the section of the document that explains what specifically needs
to be configured and why. This page is provided largely as a reference.

By default, xidump, wacdump, xsetwacom, wacom\_drv.o, and wacomcpl are
built. Additional options include replacement of kernel drivers for hid,
mousedev, evdev, and usbmouse as well as building the XFree86/Xorg
driver from scratch. Lastly, remember that for every --enable option,
there is also an equivalent --disable option.

The configuration options are listed on this page. You can also see the
online list by issuing ./configure -help under linuxwacom's base
directory.

Note: You should remove the existing linuxwacom package on your system
before installing the drivers and utilities from this project. Building
Kernel Modules - USB Only

In order to build kernel modules, you will need the kernel source
installed on your system. If you are running on Redhat or Mandrake, you
can get it by installing the kernel-source RPM.

The kernel source directory is assumed to be in /usr/src/linux,
/usr/src/linux-2.6, /usr/src/linux-\`uname -r\`, or /lib/modules/\`uname
-r\`/source. If your kernel sources are elsewhere, you will need to
specify the directory with the --with-kernel option described below.

For older 2.6 kernels, you need to configure the kernel modules (wacom
and hid) under your kernel source directory before configuring
linuxwacom.

Note, for kernel 2.6.18 and later, no need to build hid any more. Refer
to Testing Tablet Detection to see if you need to build hid or not.
Module Versioning - USB Only

The script attempts to discover if the kernel is using module versioning
by detecting the presence of version numbers in the hid.o module of the
currently active kernel. Recent package versions also check for hid.o.gz
which exist on Mandrake systems. The configure script may not be able to
determine if kernel module versioning should be enabled or not, in which
case it will say "unknown, assuming no."

If module versioning is disabled when it should be enabled, depmod will
complain about missing symbols but otherwise, things will probably work
fine. If it is enabled when it should be disabled, the code may not
compile, and it almost certainly will not load properly. If the
configure script fails to determine the correct value, the default
action of disabling module versioning is the better choice, and you can
allows enable it manually and rebuild if depmod complains. The
XFree86/Xorg XInput Driver - USB and Serial

Generally, you will not need to build wacom\_drv.o since it ships in
binary form in the prebuilt directory. There are prebuilt binaries for
XFree86 and Xorg corresponding to x86 and x86-64 systems, respectively.
If no one works for you, building from source may be your only option.
See the Building wacom\_drv.o from Scratch page for more information.
Library Dependencies - ncurses and XLib

Various utilities in the linuxwacom package require not only specific
libraries, but their development header files as well. The ncurses
package is one such example. Most distributions install the ncurses
libraries by default, but the header files are often located in a
separate package. You will need both. On Redhat 8.0, they can be found
in the ncurses-devel RPM.

Similarly, if you wish to test your tablet using xidump to view
XFree86/Xorg input events, you will need the XFree86/Xorg development
headers. On Redhat/Fedore Core, they are contained in the
XFree86-devel/xorg-sdk package.

If any packages are missing, the configuration will warn you and disable
building any programs that depend on them. Processor Type

The processor type is determined by the script and used to build the
kernel modules. If it guesses incorrectly, or you would prefer a
different setting, use the --with-arch option described below.

Linux Specific Features
=======================

The Linux wacom driver uses the Linux input subsystem, as does the USB
portions of the XFree86/Xorg driver. Consequently, if you are building
on a non-Linux system, the USB code will not work for you. This is
detected, and a comment to that effect is added to the configuration
summary. I recognize that FreeBSD and similar systems have USB support;
however, until someone can bridge the gap between the FreeBSD kernel and
the XFree86/X.org driver, the problem is largely unsolved. Contributions
are of course welcome. The Linux-specific features can be
enabled/disabled using the --with-linux argument. Configuration Options
The following options are provided as reference. Normally, you will only
need a few of these options. Some obscure systems or you want to build a
driver for another platform may need all of them. Each section of the
document identifies which options are needed and when.

| Option                        | Default    | Builds                                                                                                                                                                                                                                                     |
|-------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| --enable-wacom                | no         | wacom.o kernel driver                                                                                                                                                                                                                                      |
| --enable-wacdump              | yes        | wacdump LinuxInput event monitor                                                                                                                                                                                                                           |
| --enable-xidump               | yes        | xidump XInput event monitor                                                                                                                                                                                                                                |
| --enable-libwacomcfg          | yes        | libwacomcfg Dynamic library for xsetwacom                                                                                                                                                                                                                  |
| --enable-libwacomxi           | yes        | libwacomxi Dynamic library for wacomcpl                                                                                                                                                                                                                    |
| --enable-xsetwacom            | yes        | xsetwacom XFree86 wacom driver configuration comannd                                                                                                                                                                                                       |
| --enable-quirk-tablet-rescale | best guess | Enable tablet to screen rescale code. Note: If you have TwinView setup running on a X server 1.4 and later, and your mappping doesn't work properly, enable this option may resolve the issue.                                                             |
| --enable-quirk-Uninit-called  | best guess | Enable Uninit called                                                                                                                                                                                                                                       |
| --enable-hid                  | no         | hid.o replacement kernel driver (not normally needed)                                                                                                                                                                                                      |
| --enable-usbmouse             | no         | usbmouse.o replacement kernel driver (not normally needed)                                                                                                                                                                                                 |
| --enable-evdev                | no         | evdev.o replacement kernel driver (not normally needed)                                                                                                                                                                                                    |
| --enable-mousedev             | no         | mousedev.o replacement kernel driver (not normally needed)                                                                                                                                                                                                 |
| --enable-wacomdrv             | yes        | wacom\_drv.o XFree86/Xorg driver (binary is available in prebuilt directory)                                                                                                                                                                               |
| --enable-modver=yes/no        | best guess | enables kernel module versioning; usually guesses correctly, but can be enabled or disabled if not                                                                                                                                                         |
| --with-kernel=dir             | best guess | Specifies the kernel source directory if configure cannot guess correctly.                                                                                                                                                                                 |
| --with-x-src=dir              | best guess | Specifies the X driver build source directory                                                                                                                                                                                                              |
| --with-xorg-sdk=dir           | best guess | Specifies the Xorg SDK directory                                                                                                                                                                                                                           |
| --with-tcl=dir                | /usr       | Specifies the tcl directory. The tcl's include and lib directories should be under this directory. If tcl.h is not in dir/include, it will be searched under dir directly                                                                                  |
| --with-tk=dir                 | /usr       | Specifies the tk directory. If tk is under the same directory as tcl, this option can be eliminated. Otherwise, the tk's include and lib directories should be under this directory. If tk.h is not in dir/include, it will be searched under dir directly |
| --with-arch=arch              | best guess | Specifies the architecture if configure guesses incorrectly                                                                                                                                                                                                |
| --enable-xserver64=yes/no     | best guess | enables 64-bit X server. You probably need to define xlib directory by adding option --with-xlib=xlib-dir so compiler can link with the right Xlib.                                                                                                        |
| --with-linux=yes/no           | best guess | Specifies if compiled on a Linux system; USB code is Linux specific                                                                                                                                                                                        |
| --with-xlib=yes/no            | best guess | Specifies if building xlib-based programs; xidump uses XInput headers                                                                                                                                                                                      |
| --enable-dlloader=yes/no      | best guess | Enable dlloader build option and built wacom\_drv.so instead of wacom\_drv.o                                                                                                                                                                               |
| --with-xmoduledir=dir         | best guess | Specify wacom\_drv path explicitly. Implies --enable-dlloader                                                                                                                                                                                              |
|                               |            |                                                                                                                                                                                                                                                            |

### Configuration Samples

Here is a sample output of the script on a Fedora Core 5 system:

        [jej@ayukawa linuxwacom]$ ./configure
        checking for a BSD-compatible install... /usr/bin/install -c
        checking whether build environment is sane... yes
        ...
        checking for arch type... i386-redhat-linux
        checking for kernel type... Linux
        checking for linux-based kernel... yes
        checking for kernel sources... /lib/modules/2.6.20-1.2320.fc5/source
        checking for kernel module support... yes
        checking for kernel module versioning... yes
        checking for valid Xorg SDK... ok
        checking for X... libraries , headers
        ...
        checking for X lib directory... found
        checking for tclsh... /usr/bin/tclsh
        checking for tcl version... 8.4
        checking for tcl header files... /usr/include/
        checking for tk header files... found
        checking ncurses.h usability... yes
        checking ncurses.h presence... yes
        checking for ncurses.h... yes
        ...
        ----------------------------------------
          BUILD ENVIRONMENT:
               architecture - i386-redhat-linux
               linux kernel - yes 2.6.19
          module versioning - yes -DCONFIG_MODVERSIONS -DMODVERSIONS -include /lib/modules/2.6.20-1.2320.fc5/source/include/linux/modversions.h
              kernel source - yes /lib/modules/2.6.20-1.2320.fc5/source
                   Xorg SDK - yes /usr/include/xorg
                  XSERVER64 - no 
                   dlloader - yes 
                       XLib - yes /usr/lib
                        TCL - yes /usr/include
                         TK - yes /usr/include
                    ncurses - yes

          BUILD OPTIONS:
                    wacom.o - no
                    wacdump - yes
                     xidump - yes
                libwacomcfg - yes
                 libwacomxi - yes
                  xsetwacom - yes
                      hid.o - no
                 usbmouse.o - no
                    evdev.o - no
                 mousedev.o - no
                    input.o - no
                tabletdev.o - no
               wacom_drv.so - yes /usr/lib/xorg/modules/input
                wacom_drv.o - no
          wacom*_drv quirks - libc-wrapper key-events dixScreenOrigins
        ----------------------------------------

If the configure script fails to find something that it is looking for,
it may disable some options that you previously enabled on the
command-line. If this happens, check the output for a warning like the
following:

        checking for valid XFree86/X.org build environment... xf86Version.h missing
        Tried /usr/programs/Xserver/hw/xfree86 and /usr/xc/programs/Xserver/hw/xfree86
        ...
        ***
        *** WARNING:
        *** Unable to compile wacom_drv.{o,so}
        *** without Xorg SDK or XFree86 build environment
        *** wacom_drv.o will not be built
        ***

In this particular case, the X driver was enabled. The --with-x-src
option was not specified. And the configure can not find the header
file, xf86Version.h, under any of the predefined paths. Without the
build environment, the module cannot be compiled and was consequently
disabled.

The following sample command-line will build everything but wacdump
while disabling module versioning. It also has a user-specified kernel
source directory:

        [jej@ayukawa linuxwacom]$ ./configure --enable-wacom --disable-wacdump

        checking for a BSD-compatible install... /usr/bin/install -c
        checking whether build environment is sane... yes
        ...
        checking for HAL... yes
        checking for arch type... i586-suse-linux
        checking for kernel type... Linux
        checking for linux-based kernel... yes
        checking for kernel source/headers... /lib/modules/2.6.25.20-0.7-default/source
        checking kernel version... 2.6.25.20-0.7-default
        checking for kernel module support... yes
        checking for Xlib... yes
        checking for XSERVER... yes
        checking for xserver libc-wrapper header-files... yes
        checking if scaling tablet to screen size is needed... yes
        checking if Xorg server is version 1.4 or later... yes
        checking if Xorg is 7.3 or earlier... no
        checking if Xorg server is version 1.5.2 or later... no
        checking if Xorg server is version 1.6 or later... no
        checking if Xorg SDK defined IsXExtensionPointer... yes
        checking if Xorg SDK defines dixScreenOrigins... yes
        ...
        ----------------------------------------
          BUILD ENVIRONMENT:
               architecture - i686
               linux kernel - yes 2.6.24
          module versioning - no
              kernel source - yes /lib/modules/2.6.25.20-0.7-default/source
                    XFree86 - no
                   Xorg SDK - yes /usr/include/xorg
                  XSERVER64 - no
                   dlloader - yes
                 xf86config - no
                       XLib - yes /usr/X11R6/lib
                        TCL - yes /usr/local/ActiveTcl
                         TK - yes /usr/local/ActiveTcl
                    ncurses - yes

          BUILD OPTIONS:
                    wacom.o - yes
                    wacdump - no
                     xidump - yes
                libwacomcfg - yes
                 libwacomxi - yes
                  xsetwacom - yes
               wacom_drv.so - yes
                wacom_drv.o - no
          wacom*_drv quirks - hal libc-wrapper tablet-screen-scaling IsXExtensionPointer key-events dixScreenOrigins Uninit-called
        ----------------------------------------

Notice that the configure script guessed module versioning was enabled
by default, but was disabled by the command-line option
--disable-modver. Similarly, the wacdump program which is enabled by
default was also disabled. All the kernel modules and the XFree86 wacom
driver are enabled.

Here is another sample from Red Hat Enterprise Linux ES v.4:

        [jej@ayukawa linuxwacom-x86-64]$ ./configure \
                --enable-wacom --enable-hid \
                --with-xf86=/home/jej/Desktop/X11R6.8 \
                --with-tcl=/usr/local/ActiveTcl \
                --enable-xserver64 \
                --with-xlib=/usr/X11R6/lib64 
        checking for a BSD-compatible install... /usr/bin/install -c
        checking whether build environment is sane... yes
        ...
        checking build system type... x86_64-redhat-linux-gnu
        checking host system type... x86_64-redhat-linux-gnu
        checking for ld used by g++... /usr/bin/ld -m elf_x86_64
        ...

        ----------------------------------------
          BUILD ENVIRONMENT:
               architecture - x86-64
               linux kernel - yes 2.6.9
          module versioning - yes -DCONFIG_MODVERSIONS -DMODVERSIONS -include /usr/src/linux/include/linux/modversions.h
              kernel source - yes /usr/src/linux
                    XFree86 - yes /home/jej/Desktop/X11R6.8
                  XSERVER64 - yes
                       XLib - yes /usr/X11R6/lib64
                        TCL - yes /usr/local/ActiveTcl
                         TK - yes /usr/local/ActiveTcl
                    ncurses - yes

          BUILD OPTIONS:
                    wacom.o - yes
                    wacdump - yes
                     xidump - yes
                libwacomcfg - yes
                 libwacomxi - yes
                  xsetwacom - yes
                      hid.o - yes
               wacom_drv.so - no
                wacom_drv.o - yes
          wacom*_drv quirks - libc-wrapper key-events
        ----------------------------------------

Next: The [USB Kernel driver](/wiki/USB_Kernel_driver "wikilink")