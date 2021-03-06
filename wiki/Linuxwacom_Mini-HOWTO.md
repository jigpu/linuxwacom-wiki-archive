---
title: Linuxwacom Mini-HOWTO
permalink: wiki/Linuxwacom_Mini-HOWTO/
layout: wiki
tags:
 - Linuxwacom
---

This section outlines the Wacom Driver updating steps on a Fedora 9 x86
system. It is mainly here for those who don't want to go through the
detailed HOWTO. For specific helps on other platforms/distributors,
search the distributor's disscussion forum; search the [general
discussion](https://lists.sourceforge.net/lists/listinfo/linuxwacom-discuss)
list; or Google with the key words of "your platform, your distributor,
linux, and wacom" will most probably bring you enough information to get
started.

First, download the most recent linuxwacom driver package from
[Downloading the Code](/wiki/Linuxwacom_HOWTO#Downloading_the_Code "wikilink")
page to your home directory and unpack it:

        [jej@ayukawa jej]$ bunzip2 linuxwacom-0.8.8-10.tar.bz2
        [jej@ayukawa jej]$ tar xvf linuxwacom-0.8.8-10.tar

<span style="background:#00ffff"> Note, if you have a Serial tablet and
Tablet PC, there is no need to update your kernel driver. </span>

Before updating your system with linuxwacom driver, you need to make
sure your Fedora 9 is up to date. To do so, issue yum update. You most
likely need to reboot your system to use the updated kernel. The
following steps are based on kernel 2.6.26.

<span style="background:#00ffff"> After updating your kernel, those of
you who have installed other customer kernel drivers, such as nVidia
graphic card driver, will need to reinstall the drivers again. </span>

         
        [jej@ayukawa jej]$ su
        Password:
        [root@ayukawa jej]# yum remove linuxwacom
        [root@ayukawa jej]# yum install xorg-x11-server-devel
        [root@ayukawa jej]# yum install kernel-devel
        [root@ayukawa jej]# yum install automake
        [root@ayukawa jej]# yum install gcc
        [root@ayukawa jej]# yum install libtool
        [root@ayukawa jej]# yum install libXi
        [root@ayukawa jej]# yum install libXi-devel.i386
        [root@ayukawa jej]# yum install ncurses
        [root@ayukawa jej]# yum install ncurses-devel
        [root@ayukawa jej]# yum install tcl
        [root@ayukawa jej]# yum install tcl-devel
        [root@ayukawa jej]# yum install tk
        [root@ayukawa jej]# yum install tk-devel
        [root@ayukawa jej]# cd linuxwacom-0.8.8-10 
        [root@ayukawa linuxwacom-0.8.8-10]# ./configure --enable-wacom
        [root@ayukawa linuxwacom-0.8.8-10]# make
        [root@ayukawa linuxwacom-0.8.8-10]# cp /lib/modules/`uname -r`/kernel/drivers/usb/input/wacom.ko wacom.ko_`uname -r`
        [root@ayukawa linuxwacom-0.8.8-10]# cp src/2.6.30/wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet
        [root@ayukawa linuxwacom-0.8.8-10]# make install

Then check if there is a file with the word wacom in its name under
/etc/udev/rules.d (refer to [Wacom Kernel
Driver](/wiki/Linuxwacom_HOWTO#Updated_wacom.c "wikilink") for detailed
information). And make certain that the following lines are in the file:

         
        BUS=="usb", KERNEL=="event*", SYSFS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        BUS=="usb", KERNEL=="event*", SYSFS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

Now, the driver upgrading part is done. You need to update
/etc/X11/xorg.conf file to load the driver. Open /etc/X11/xorg.conf with
your favorite editor (vi, gedit, emacs...) and add proper lines as
stated at [Configuring X
driver](/wiki/Linuxwacom_HOWTO#Configuring_X11 "wikilink").

To add a fallback layer to the process, open /etc/inittab in an editor
and change id:5:initdefault to id:3:initdefault. ONLY change 5 to 3 in
the file of that line! This step is for testing purpose only.

Plug your tablet in if you haven't done so. Reboot your system. When the
system is up, login as yourself, and issue startx. If everything works,
i.e., cursor follows the pen tip on the tablet, etc, you are done. You
can change id:3 back to id:5 in your /etc/inittab now.

Otherwise, post your problem to the [general
discussion](https://lists.sourceforge.net/lists/listinfo/linuxwacom-discuss)
mailing list.

<center>
<small> Copyright (C) 2002-2011 - LinuxWacom

This website and its contents are licensed under the GNU GENERAL PUBLIC
LICENSE. </small>

</center>
