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

See [How Wacom tablets work](/wiki/How_Wacom_tablets_work "wikilink")

Getting It Together
===================

See [Building linuxwacom](/wiki/Building_linuxwacom "wikilink")

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
