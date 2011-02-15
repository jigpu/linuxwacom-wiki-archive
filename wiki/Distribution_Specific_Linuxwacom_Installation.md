---
title: Distribution Specific Linuxwacom Installation
permalink: wiki/Distribution_Specific_Linuxwacom_Installation/
layout: wiki
tags:
 - Linuxwacom
---

Installing wacom driver On Debian
---------------------------------

The following documentation for building wacom driver on Debian was
written by Olivier Lecarme.

Copyright (C) June 14, 2005 Olivier Lecarme.

I'm using the Sid version of Debian, but I was told that the Sarge
distribution, recently become the stable one, already contains what is
necessary. Version 2.6.11 of the kernel is the highly preferred one by
people maintaining the Linux Wacom software.

**Download the 2.6.11 kernel**

I was completely unsuccessful when trying to configure and compile my
own kernel: certainly I omitted some capital module, but I could not
decide what it was. Thus, I chose the last pre-compiled kernel.

       1.   apt-get kernel-image-2.6.11-1-686

       2.   apt-get kernel-headers-2.6.11-1-686

If you use Lilo, configure your /etc/lilo.conf file, taking into account
that this kernel needs an initrd= line.

**Optionally**

If your case is irrelevant, please skip this section and the following
one.

       1.      I Have a Broadcom NetXtreme BCM5751 Ethernet card, which needs the tigon3 driver, unavailable in Debian kernel 2.6.11. The solution is here.

          Thus I got the package:

             1.   cd /usr/src
             2.   wget http://www.acm.rpi.edu/~dilinger/kernel-source-nonfree-2.6.11/kernel-nonfree-modules-2.6.11-1-686-2.6.11-1_i386.deb

       2.      I have an ATI X 300 video card, whose driver is not available in Debian kernel 2.6.11. The solution is here.

          Thus I got the packages:

             1.    for the driver:  wget http://www.stanchina.net/~flavio/debian-fglrx-xfree86/fglrx-driver_8.12.10-1_i386.deb
             2.    and for the kernel module:  wget http://www.stanchina.net/~flavio/debian-fglrx-modules/fglrx-kernel-2.6.11-1-686-smp_8.12.10-1+2.6.11-2_i386.deb

**Install the optional packages**

       1.   cd /usr/src

       2.   dpkg -i kernel-nonfree-modules-2.6.11-1-686_2.6.11-1_i386.deb

       3.   dpkg -i fglrx-driver_8.12.10-1_i386.deb

       4.   dpkg -i fglrx-kernel-2.6.11-1-686_8.12.10-1+2.6.11-2_i386.deb

**Install the wacom tools package**

       1.   apt-get install wacom-tools

       2.   dpkg-reconfigure wacom-kernel-source

Since your debconf configuration probably did not ask for the lowest
priority questions, this step is needed. You might also use it later if
you update your kernel, for instance.

When asked whether you want to build the modules, answer yes, and tell
where the headers are located (normally
/usr/src/kernel-headers-2.6.11-1-686).

**Prepare for the /dev/input/wacom link**

In /etc/udev/rules.d/10-wacom.rules, add the following line:

    KERNEL="event*", SYSFS{idVendor}="056a", NAME="input/%k", SYMLINK="input/wacom%e" 

Thus the drivers will find the Wacom tablet, whatever its
/dev/input/eventX address is.

**Change your XF86Config-4 file**

The important sections are:

       1. The ServerLayout section:

    Section "ServerLayout"
            [ ... ]
        InputDevice    "stylus"    "SendCoreEvents"
        InputDevice    "eraser"    "SendCoreEvents"
        InputDevice    "cursor"    "SendCoreEvents"
        InputDevice    "pad"
    EndSection

       2. The InputDevice sections:

    Section "InputDevice"
        Identifier   "stylus"
        Driver       "wacom"
        Option       "Type" "stylus"
        Option       "USB" "on"
        Option       "Threshold" "10"
        Option       "Device" "/dev/input/wacom"
    EndSection

    Section "InputDevice"
        Identifier   "eraser"
        Driver       "wacom"
        Option       "Type" "eraser"
        Option       "USB" "on"
        Option       "Threshold" "10"
        Option       "Device" "/dev/input/wacom"
    EndSection

    Section "InputDevice"
        Identifier   "cursor"
        Driver       "wacom"
        Option       "Type" "cursor"
        Option       "USB" "on"
        Option       "Threshold" "10"
        Option       "Device" "/dev/input/wacom"
    EndSection

    Section "InputDevice"
       Identifier "pad"
       Driver "wacom"
       Option "Device" "/dev/input/wacom"
       Option "Type" "pad"
       Option "USB" "on"
    EndSection 

       3. The section dealing with your normal mouse must be considered too. See [[Linuxwacom HOWTO#Mouse1 (for some 2.6 systems)|Mouse1 (for some 2.6 systems)]].  I didn't use /dev/psaux nor /dev/input/mice.
          /dev/input/mouse0 works for a PS/2 mouse. For a USB mouse, there is a problem mentioned in the '''Remaining problems'''.

       4. If you use the ATI X300 card, you need to change also the Device section:
        
    Section "Device"
            Identifier      "ATI"
            Driver          "fglrx"
            Option "VideoOverlay" "on"
            Option "OpenGLOverlay" "off"
            Option "UseInternalAGPGART" "no"
    EndSection

**Final steps**

1. Reboot. This will also restart the X server, of course. 2. Enjoy!

**If you want to use the expresskeys of your Intuos tablet**

If you do nothing more, the pad is not usable at all. However, you will
find here a specific tool for having the pad available in various
programs, for example XTerm, Gimp, or Blender.

This tool is very easy to compile, install, and use. If you want to use
the pad in Gimp, do not enable it in "Preferences -&gt; Input Devices
-&gt; Configure the extended input devices", contrarily to the three
other tools (stylus, eraser, and cursor). You can easily configure what
the expresskeys send, according to your tastes, and use only them for
the tasks that need a good control of the stylus or eraser.

**Remaining problems**

If your tablet is always plugged, everything works perfectly. If you
want to plug it out, for example in order to move it onto another
computer, and then to plug it back later, problems begin.

For the present, you must take care of the following things:

1. If you plug in the tablet again, the corresponding driver is not
informed of this, thus you must restart the X server.

2. If the tablet is plugged when you reboot, and you have an USB mouse,
maybe the /dev/input address of this mouse is not the same as the
previous time. Thus you will have to change this in your XF86Config-4
file.

3. If you use GDM or KDM or XDM, you should plug in the tablet after
rebooting, but before the X server starts, which is somewhat difficult!
Thus you will be forced to restart the X server, i.e. kill it
Ctrl+Alt+Backspace) and not simply logging out.

**Final remarks**

  
I would like to thank all persons who helped me, on the LinuxWacom or
the Gimp-user discussion lists, especially Carol Spears, Karine Delvare,
Ping Cheng, and Ron.

Building wacom driver On Fedora 13
----------------------------------

The following steps for building wacom driver on Fedora 13 were
extracted from the emails of Jonathan R Young and Peter Hutterer. If you
have any problems or questions, go ahead and post them to the list.

**Build kernel driver wacom.ko**

    yum-buildep linuxwacom
    wget http://prdownloads.sourceforge.net/linuxwacom/linuxwacom-0.8.8-10.tar.bz2

    tar -xf linuxwacom-0.8.8-10.tar.bz2
    cd  linuxwacom-0.8.8-10
    ./configure --enable-wacom
    su -c "cp src/2.6.30/wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet"

**Build X driver wacom\_drv.so**

Refer to
<http://sourceforge.net/apps/mediawiki/linuxwacom/index.php?title=Xf86-input-wacom>
for details.

    wget http://prdownloads.sourceforge.net/linuxwacom/xf86-input-wacom-0.10.8.tar.bz2
    ./configure --prefix=/usr  
    make
    su -c "make install"

Finally, plug the tablet in and restart the X server (or just restart
your system).

Building wacom driver On RHEL 5 / CentOS 5
------------------------------------------

The following documentation for building wacom x.org and kernel modules
on RHEL 5 / CentOS 5 was written by Sylvain Giasson. If you have any
problems or questions, go ahead and post them to
linuxwacom-discuss@lists.sourceforge.net.

Copyright (C) August 2009 Sylvain Giasson

Summary

When installing a driver, you will not always get an rpm package to
install it. You may have to compile it for the kernel and architecture
you are using. This document will show you how to compile the wacom
driver from source.

Download the binaries

You will need to download the source for the wacom driver. You need to
go on the linux wacom web page: <http://linuxwacom.sourceforge.net>. In
this example, you download it into /usr/tmp.

Uncompress the binaries

Once downloaded, the wacom driver source package are TARed and
compressed as bz2 archives. They must extracted and uncompressed unsing
the following commands.

`     su - root`  
`     cd /usr/tmp`  
`     tar xjvf linuxwacom-0.8.4.tar.bz2`

Note: tar options: x=extract; j=extract bz2 before tar; v=verbose; f=the
file you are going to uncompress.

Updating/installing pre-requisite packages with yum before compiling

Before compiling the driver for your workstation, you need to be sure to
have some pre-requisites modules installed. The easiest way to install
them is by using yum.

This utility allows you to automatically download and install the kernel
headers and development package, the X server software development kit
and source, as well as the libXi development package. (Note: Your
workstation must have internet access).

`     yum install kernel-devel kernel-headers `  
`     yum install xorg-x11-server-source xorg-x11-server-sdk libXi-devel`

Once yum found everything it needs, press "y" to begin the installation
process.

Compiling the driver

`  1. Compiling the driver is simple.  You need to configure a custom installer, create it,  and `

install it. Type the following commands:

`     su - root`  
`     cd /usr/tmp/linuxwacom-0.8.4`  
`     ./configure --enable-wacom`  
`      `

If some dependencies aren't resolved, the configure step will not be
completed successfully. Once dependancies are resolved, run ./configure
again.

`  2. When the "configure" step succeeds, continue to the next steps. Type:`  
`      `  
`     ./make`  
`      `  
`  3. This will create the actual installer using the configuration from the previous step. Type:`  
`      `  
`     ./make install`  
`      `  
`     This will install the driver.`  
`      `  
`  4. Install kernel driver (create a backup copy)`  
  
``      cp /lib/modules/`uname -r`/kernel/drivers/usb/input/wacom.ko  /lib/modules/`uname -r`/kernel/drivers/usb/input/wacom.ko.old.$$ ``  
``      cp src/`uname -r | cut -d- -f1`/wacom.ko /lib/modules/`uname -r`/kernel/drivers/usb/input/ ``

`  5. Reboot the workstation `

Building wacom driver On Suse 9.2
---------------------------------

Nico Kadel-Garcia has provided a changed SPEC file for SuSE 9.2. You can
download the spec here.

"There are only a few needed changes: use the new software, throw out an
old patch, teach it to use the right options for x86\_64 compilation,
and stop it from generating symlinks into /usr/include/X11 at
compilation time, and it's done. ". Nico said on Mar 21 2005.

Building wacom driver On Ubuntu 9.04 64-bit system
--------------------------------------------------

The steps on this page are taken from the following posts:-

<http://ubuntuforums.org/showthread.php?t=1215979&page=3> \#22
<http://ubuntuforums.org/showthread.php?t=1038949&page=11> \#104 Section
1 <http://ubuntuforums.org/showthread.php?t=967147&page=18> \#176

Craig Stevens summarized the steps below for his Wacom Intuos4 M on a
64-bit Ubuntu 9.04 (Aug. 26, 2009). Craig also wanted to credit Favux
and Eric Honaker for their help. If you have any problems or questions,
please post them to linuxwacom-discuss@lists.sourceforge.net.

Process from start to finish:-

`  1. Installed "wacom-tools" and "xserver-xorg-input-wacom" using synaptic.`  
`  2. "cd ./Desktop"`  
`  3. "wget `[`http://prdownloads.sourceforge.net/linuxwacom/linuxwacom-0.8.4.tar.bz2`](http://prdownloads.sourceforge.net/linuxwacom/linuxwacom-0.8.4.tar.bz2)`"`  
`  4. "sudo apt-get update"`  
`  5. "sudo apt-get install build-essential libx11-dev libxi-dev x11proto-input-dev xserver-xorg-dev tk8.4-dev tcl8.4-dev libncurses5-dev"`  
`  6. "sudo apt-get upgrade"`  
`  7. Then got the kernal version with: "uname -r" (needed in steps 8 and 14)`  
`  8. I had the generic kernel so:`  
`   "sudo apt-get install linux-headers-generic" `  
`       (or it would have been: "sudo apt-get install linux-headers-rt for the rt kernel")`  
`  9. "tar xjvf linuxwacom-0.8.4.tar.bz2"`  
` 10. "cd linuxwacom-0.8.4"`  
` 11. "./configure --enable-wacom"`  
` 12. "make"`  
` 13. "sudo make install"`  
` 14. Then copied the wacom.ko built in step 12 over the existing one in lib/modules..., `  
``    "sudo cp ./src/2.6.28/wacom.ko /lib/modules/`uname -r`/kernel/drivers/input/tablet/wacom.ko"  ``  
``        where `uname -r` is the kernel version from step 7. ``  
` 15. Changed /usr/share/hal/fdi/policy/20thirdparty/10-wacom.fdi for a new one attached to `  
`   `[`http://ubuntuforums.org/showthread.php?t=967147&page=18`](http://ubuntuforums.org/showthread.php?t=967147&page=18)` #176 which clearly explains the procedure `  
`       (just swapping out the file contents in a text editor, after backing up!)`  
` 16. In a text editor added the line "wacom" to the end of the /etc/modules file`  
` 17. Rebooted and it worked.`

This has got the tablet as far as working like a mouse in Ubuntu Gnome
desktop and also working in Photoshop in XP on a VirtualBox VM. The
Touch Ring zooms in Firefox.

<center>
<small> Copyright (C) 2002-2011 - LinuxWacom

This website and its contents are licensed under the GNU GENERAL PUBLIC
LICENSE. </small>

</center>
