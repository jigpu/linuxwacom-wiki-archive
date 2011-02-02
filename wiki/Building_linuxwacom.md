---
title: Building linuxwacom
permalink: wiki/Building_linuxwacom/
layout: wiki
tags:
 - linuxwacom
---

**Note: this is a guide for building [linuxwacom](linuxwacom "wikilink")
and does not apply to [xf86-input-wacom](xf86-input-wacom "wikilink").**

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
