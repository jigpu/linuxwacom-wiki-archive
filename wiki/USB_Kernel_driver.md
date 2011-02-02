---
title: USB Kernel driver
permalink: wiki/USB_Kernel_driver/
layout: wiki
---

Testing Tablet Detection
------------------------

In this section we will determine which driver, if any, claims control
over the tablet. There are at least two drivers that are interested:

1.  (usb)hid.o which may think it is an HID device (for kernel 2.6.17 or
    older),
2.  the wacom driver which should identify the tablet as its own.

To see which driver is driving the tablet, issuing *more
/proc/bus/usb/devices* should list something similar to the following:

        [jej@ayukawa wacom]$more /proc/bus/usb/devices
        T:  Bus=01 Lev=01 Prnt=01 Port=01 Cnt=02 Dev#=  3 Spd=12  MxCh= 0
        D:  Ver= 1.10 Cls=00(>ifc ) Sub=00 Prot=00 MxPS= 8 #Cfgs=  1
        P:  Vendor=056a ProdID=0042 Rev= 1.15
        S:  Manufacturer=Tablet
        S:  Product=XD-0608-U
        C:* #Ifs= 1 Cfg#= 1 Atr=80 MxPwr=140mA
        I:  If#= 0 Alt= 0 #EPs= 1 Cls=03(HID  ) Sub=01 Prot=02 Driver=wacom
        E:  Ad=81(I) Atr=03(Int.) MxPS=  10 Ivl=5ms

where *Vendor=056a* indicates a Wacom device. *Driver=wacom* means the
Wacom driver is in control of the tablet. If you see anything other than
wacom after *Driver=*, your model is not supported in your current
kernel and the kernel driver [ needs to be
updated](/wiki/Updating_the_kernel_driver "wikilink").

Alternatively, *more /proc/bus/input/devices* gives you

        [jej@ayukawa wacom]$more /proc/bus/input/devices
        I: Bus=0003 Vendor=056a Product=0042 Version=1.15
        N: Name="Wacom Intuos2 6x8"
        P: Phys=usb-0000:00:1d.1-2/input0
        H: Handlers=event3
        B: EV=1f
        B: KEY=1cff 0 1f00ff 0 0 0 0 0 0 0 0
        B: REL=100
        B: ABS=f00017b
        B: MSC=1

where, again, *Vendor=056a* indicates a Wacom device. *Name="Wacom
Intuos2 6x8"* means an Intuos2 6x8 tablet reported to
*/dev/input/event3*. If there is no Wacom after *Name=*, you need to
[update the kernel driver](/wiki/Updating_the_kernel_driver "wikilink").

On older 2.6 kernel systems, unplug then replug your tablet after
issuing tail -f /var/log/messages, you should see a flurry of activity.
The exact output depends a lot on your particular kernel and
distribution.

`   This is Suse 10.2 (2.6.25.20-0.7):`

        [jej@ayukawa usb]# tail /var/log/messages
        usb 2-2: USB disconnect, address 2
        usb 2-2: new full speed USB device using uhci_hcd and address 3
        usb 2-2: configuration #1 chosen from 1 choice
        input: Wacom Intuos4 6x9 as /devices/pci0000:00/0000:00:1f.4/usb2/2-2/2-2:1.0/input/input8
        usb 2-2: New USB device found, idVendor=056a, idProduct=00b9
        usb 2-2: New USB device strings: Mfr=1, Product=2, SerialNumber=0
        usb 2-2: Product: PTK-640
        usb 2-2: Manufacturer: Tablet

If all went well like above, the USB device was successfully detected
and handled by the wacom driver. This presumably means that information
like pressure and tilt will be received on */dev/input/event3*.

If instead you got any of the following lines in your log, the wacom
driver did not get control. Either your wacom kernel driver doesn't
support your tablet or hid is in control.

            input0,hiddev0: USB HID v1.00 Mouse [Tablet PTK-640] on usb1:5.0
            input0: Tablet PTK-640 on usb1:5.0

Building wacom.c
----------------

To build the wacom.ko kernel module, you need to configure wacom as a
kernel module under your kernel source tree first. The kernel sources
are required as described on the configuration page.

Then, you need to configure the package with --enable-wacom option.
Here's how the configuration should generally look:

        [jej@ayukawa linuxwacom]$ ./configure --enable-wacom
        ...
        checking for valid kernel source tree... ok
        ...
        ----------------------------------------
          BUILD ENVIRONMENT:
               architecture - i686
               linux kernel - yes 2.6.9
          module versioning - yes
                     kernel - yes /usr/src/linux
                    XFree86 - no
                       XLib - yes /usr/X11R6
                        TCL - yes /usr
                         TK - yes /usr
                    ncurses - yes
                        GTK - 2.0.6

          BUILD OPTIONS:
                    wacom.o - yes
                    wacdump - yes
                     xidump - yes
                libwacomcfg - yes
                 libwacomxi - yes
                  xsetwacom - yes
                      hid.o - no
                wacom_drv.o - no
        ----------------------------------------

As shown above, the kernel directory was detected and the wacom kernel
module will be built. If the kernel option shows "no", you will need to
specify the --with-kernel option and the correct directory.

`   For those who feel comfortable to build everything from the source tree, please skip the make and install steps below. Scroll down to the end of this page to see the steps with light blue background. `

If you wanted to build the kernel driver and you do not see the yes
after "wacom.o -" in the the "BUILD OPTIONS:", run 'make oldconfig; make
prepare; make' on your kernel source should fix the issue.

To build the driver, just run make.

If everything works properly, you'll see the following from the make:

        [jej@ayukawa linuxwacom]$ make
        ...
        Making all in 2.6.9
        make[3]: Entering directory `/home/jej/linuxwacom/src/2.6.9'
            Building linuxwacom drivers for 2.6 kernel.
        make -C /usr/src/linux M=/home/jej/linuxwacom/src/2.6.9
        make[4]: Entering directory `/home/jej/linux-2.6.9'
          LD      /home/jej/linuxwacom/src/2.6.9/built-in.o
          CC [M]  /home/jej/linuxwacom/src/2.6.9/wacom.o
          Building modules, stage 2.
          MODPOST
          CC      /home/jej/linuxwacom/src/2.6.9/wacom.mod.o
          LD [M]  /home/jej/linuxwacom/src/2.6.9/wacom.ko
        make[4]: Leaving directory `/usr/src/linux'
        make[3]: Leaving directory `/home/jej/linuxwacom/src/2.6.9'
        ...

This part is for those who want to manually build the wacom kernel
driver in source tree. If you already followed the steps above, you can
move on to next page.

Please backup wacom.c in your kernel tree first. Then copy wacom.c (or
wacom\_wac.c, wacom\_wac.h, wacom\_sys.c, and wacom.h if defined) from
the related linuxwacom directory to the source tree (if 4 files were
copies, you need to add wacom-objs := wacom\_sys.o wacom\_wac.o to the
Makefile under your kernel source input directory) and rebuild the
kernel. An example for kernel 2.6.9 is as following:

        [jej@ayukawa linuxwacom]$ cp /usr/src/linux/drivers/usb/input/wacom.c /usr/src/linux/drivers/usb/input/wacom.c.2.6.9     
        [jej@ayukawa linuxwacom]$ cp src/2.6.9/wacom.c /usr/src/linux/drivers/usb/input/     
        [jej@ayukawa linuxwacom]$ cd /usr/src/linux      
        [jej@ayukawa linux]$ make    
        [jej@ayukawa linux]$ su      
        [jej@ayukawa linux]#make install     
        [jej@ayukawa linux]#make modules_install     
        [jej@ayukawa linux]#reboot   

Testing If wacom.(k)o Will Load
-------------------------------

Before we install the wacom driver, we need to test that it will load
properly. We do this by loading the driver manually. We will also need
to be root to do this.

**WARNING:** there is a small chance that this will bomb your kernel, so
we run sync to write all the stale buffers to the disk. People using
ext3 have little to worry about, but it's always good to be prepared for
the worst. At the very least, save your work.

        [root@ayukawa linuxwacom]# sync

From the package's associated kernel directory, we unload any previous
modules and load the new one. The following example is from a kernel
2.6.24 system.

        [root@ayukawa linuxwacom]# cd src/2.6.24
        [root@ayukawa 2.6.24]# /sbin/rmmod wacom
        [root@ayukawa 2.6.24]# /sbin/insmod ./wacom.ko

Well, if you did not bomb, then good. And if you did, well, sorry. So
far, we have not had any reports of this happening, so please send in
yours.

Incidentally, if you run "/sbin/insmod wacom.ko" and happen to be in the
wrong directory, the old driver reloads, sometimes without warning. I
therefore changed this to read "/sbin/insmod ./wacom.ko" which seems to
prevent this from happening. To be certain, you can check the log file
for the correct version number.

        [root@ayukawa src]# tail /var/log/messages
        May 04 20:34:41 ayukawa kernel: usb.c: registered new driver wacom
        May 04 20:34:41 ayukawa kernel: Reporting max 30480, 31680
        May 04 20:34:41 ayukawa kernel: wacom.c: Setting tablet report for tablet data
        May 04 20:34:41 ayukawa kernel: input0: Wacom Intuos2 12x12 on usb2:3.0
        May 04 20:34:41 ayukawa kernel: wacom.c: $1.43-0.8.6-1 Vojtech Pavlik <vojtech@suse.cz>
        May 04 20:34:41 ayukawa kernel: wacom.c: USB Wacom tablet driver

The important detail is the version number. A version number like "1.52"
is an original kernel version and not from the linuxwacom package. The
correct version should also have the -j\#.\# or -pc\#.\# portion as
well. This is to help differentiate between the stock kernel driver and
those available from the Linux Wacom Project.

If you get errors inserting the module, then you may need to reconfigure
and build with module versioning disabled. If it loads without a hitch,
move on to the next part.

Installing wacom.(k)o
---------------------

To install or not to install, that is the question. Since the driver is
in memory, you can pretty much use it this way throughout the rest of
this document. Anywhere you see "modprobe wacom", you'll instead need to
"insmod ./wacom.ko". You'll also need to be careful that you are in the
package's src directory. If you instead use the less-specific command
"insmod wacom.ko" from a directory other than the package's src
directory, insmod will load the driver from the kernel modules directory
instead. The result is that you'll be using the wrong driver.

Why would you not install the driver? Well, for one, you may be building
a driver against a wrong kernel source, and if the system crashes (you
get an Oops or things come unglued in other ways), it would be nice to
reboot and have the original drivers load instead.

When should I install the driver? When you're comfortable that the
driver will not crash your system. If you really know what you're doing,
just load the drivers manually like in the previous section Testing If
wacom.(k)o Will Load.

On some distributions, Mandriva (a.k.a Mandrake) included, the wacom.ko
driver that appears in the kernel modules directory appears to be
compressed. If you cannot find wacom.ko using the method below, try
locating wacom.ko.gz instead. People who encountered this problem were
able to run gzip on the module and copy that instead.

Installing the driver requires knowing where it belongs. A little
research will help here. By using the locate command, you can find all
copies of the original driver on the computer.

        jej@ayukawa wacom]$ locate wacom.ko
        /lib/modules/2.6.17-1.2157_FC5/kernel/drivers/usb/input/wacom.ko
        /lib/modules/2.6.17-1.2174_FC5/kernel/drivers/usb/input/wacom.ko

        [jej@ayukawa wacom]$ uname -r
        2.6.17-1.2157_FC5

On this computer, there are two kernels installed. uname identifies the
currently active kernel as 2.6.17-1.2157\_FC5. The correct driver to
replace is therefore at
/lib/modules/2.6.17-1.2157\_FC5/kernel/drivers/usb/input/wacom.ko. You
will need to be root to replace this file, and it is a very good idea to
make a backup copy.

        [jej@ayukawa wacom]$ su
        [jej@ayukawa root]# cd /lib/modules/2.6.17-1.2157_FC5/kernel/drivers/usb/input
        [jej@ayukawa usb]# cp wacom.ko /home/jej/linuxwacom/src/2.6.16/wacom_old.ko
        [jej@ayukawa usb]# cp /home/jej/linuxwacom/src/2.6.16/wacom.ko wacom.ko

Here, I've saved the original to wacom\_old.ko and copied my new driver
over it. You should substitute directory names as appropriate.

**NOTE**: Don't leave the backup copy in the same directory as the
original. depmod will treat both as valid drivers, regardless of their
names. Copy the original somewhere outside of the kernel module
directory to ensure that this does not happen. In at least one case, the
backup driver was loaded instead of the new one due to a curious
dependency issue.

Finally, it is always a good thing to update the module dependencies.
This is where you find out if the module was compiled without kernel
module versioning. The following command, even if it generates errors is
relatively benign. If it fails, then there is no harm done. It just
means that you will have to load modules in the correct order since the
system will not be able to guess for you.

        [jej@ayukawa usb]# depmod -e

If you get no errors and no output, everything is fine, and the module
was compiled, linked, and installed properly. If you received unresolved
symbols like usb\_set\_idle or printk, then you need to reconfigure with
module versioning enabled and recompile.

Here is an example from a 2.6.12 system on Mandriva.

        jej@ayukawa wacom]$ locate wacom.ko.gz
        /lib/modules/2.6.12-12mdksmp/kernel/drivers/usb/input/wacom.ko.gz
        /lib/modules/2.6.12-12mdksmp/kernel/drivers/usb/input/wacom.ko.gz

        [jej@ayukawa wacom]$ uname -r
        2.6.12-12mdksmp

Loading the wacom Driver
------------------------

For Kernel 2.6.x, replace wacom.o with wacom.ko where it is used.

If you have installed the driver, now is the time to test whether it
will load when needed. If you have not installed it, but are instead
using insmod, substitute insmod mydir/src/wacom.o where you see modprobe
wacom below. It is important that you use the correct wacom.o file, the
one you just built, since insmod may load the old driver if it cannot
find the one you have specified.

        [jej@ayukawa usb]# rmmod wacom
        [jej@ayukawa usb]# modprobe usb-uhci      (or usb-ohci)
        [jej@ayukawa usb]# modprobe input
        [jej@ayukawa usb]# modprobe mousedev
        [jej@ayukawa usb]# modprobe wacom         (or insmod mydir/src/wacom.o)
        [jej@ayukawa usb]# modprobe evdev

Check the system log for status messages pertaining to the wacom. Here's
a copy of the messages from my version of the driver.

        [jej@ayukawa usb]# grep -i wacom /var/log/messages | tail
        May 04 21:23:35 ayukawa kernel: usb.c: registered new driver wacom
        May 04 21:23:35 ayukawa kernel: wacom.c: v1.43-0.8.6-1 Vojtech Pavlik <vojtech@suse.cz>
        May 04 21:23:35 ayukawa kernel: wacom.c: USB Wacom Graphire and Wacom Intuos tablet driver (MODIFIED)

The original driver was version 1.43. This version number is
1.43-0.8.6-1 so the correct driver was loaded.

Building (usb)hid.ko
--------------------

In the linuxwacom-0.8.6-1.tar.bz2 file, you will find hid-core.c, which
have special exceptions for wacom. This file is not built by default, so
you will need to reconfigure the package and run make again.

Note, for kernel 2.6.18 and later, no need to build hid any more. For
other kernels, refer to Testing Tablet Detection to see if you need to
build hid or not.

You need to configure usbhid as a module under your kernel source tree
before configuring linuxwacom.

On some distributions, Mandrake or Mandriva included, the (usb)hid.ko
driver that located in the kernel modules directory appears to be
compressed. You need to run gzip on the module and copy (usb)hid.ko.gz
instead.

        [jej@ayukawa wacom]$./configure --enable-hid --with-kernel=your-kernel-src-dir
        ...
          BUILD OPTIONS:
                      hid.o - yes
        ...

Kernel 2.6.11 and 2.6.12 are in src/2.6.11. Kernels 2.6.16 and 2.6.17
are handled in src/2.6.16. Kernels 2.6.18/19/20/21/22 are in src/2.6.18.
All the other kernels are processed in its own src/2.6.x. New 2.6
directories will be created when compatibility issue occurs.

`   For those who like to build everything from the source tree, please skip the make and install steps below. Scroll down to the end of this page to see the steps with light blue background. `

If everything works properly, you'll see the following from the make:

        [jej@ayukawa linuxwacom]$ make
        ...
        Making all in 2.6.9
        make[3]: Entering directory `/home/jej/linuxwacom/src/2.6.9'
            Building linuxwacom drivers for 2.6 kernel.
        make -C /usr/src/linux M=/home/jej/linuxwacom/src/2.6.9
        make[4]: Entering directory `/home/jej/linux-2.6.9'
          LD      /home/jej/linuxwacom/src/2.6.9/built-in.o
          CC [M]  /home/jej/linuxwacom/src/2.6.9/hid-core.o
          CC [M]  /home/jej/linuxwacom/src/2.6.9/hid-input.o
          LD [M]  /home/jej/linuxwacom/src/2.6.9/usbhid.o
          Building modules, stage 2.
          MODPOST
          CC      /home/jej/linuxwacom/src/2.6.9/usbhid.mod.o
          LD [M]  /home/jej/linuxwacom/src/2.6.9/usbhid.ko
        make[4]: Leaving directory `/usr/src/linux'
        ...

Then, use the following steps to install the driver:

        [jej@ayukawa linuxwacom]$su
        [jej@ayukawa linuxwacom]#cd src/2.6.9
        [jej@ayukawa 2.6.9]#cp usbhid.ko /lib/modules/your-kernel-ver/kernel/drivers/usb/input
        [jej@ayukawa 2.6.9]#reboot

This part is for those who want to manually build the kernel drivers
from source tree. If you already followed the steps above, you can move
on to next page.

Please backup your related kernel files first then copy the source from
the related linuxwacom directory to the source tree and rebuild the
kernel. An example for kernel 2.6.9 is as following:

        [jej@ayukawa linuxwacom]$ cp /usr/src/linux/drivers/usb/input/hid-core.c /usr/src/linux/drivers/usb/input/hid-core.c.2.6.9
        [jej@ayukawa linuxwacom]$ cp src/2.6.9/hid-core.c /usr/src/linux/drivers/usb/input/
        [jej@ayukawa linuxwacom]$ cd /usr/src/linux
        [jej@ayukawa linux]$ make
        [jej@ayukawa linux]$ su
        [jej@ayukawa linux]# make install
        [jej@ayukawa linux]# make modules_install
        [jej@ayukawa linux]# reboot

Unknown Tablet?
---------------

To determine whether your device is listed in the driver, we need to
determine the device identifier. It can be discovered by issuing more
/proc/bus/usb/devices:

        [jej@ayukawa linuxwacom]# more /proc/bus/usb/devices
        T:  Bus=01 Lev=01 Prnt=01 Port=01 Cnt=02 Dev#=  3 Spd=12  MxCh= 0
        D:  Ver= 1.10 Cls=00(>ifc ) Sub=00 Prot=00 MxPS= 8 #Cfgs=  1
        P:  Vendor=056a ProdID=0044 Rev= 1.15
        S:  Manufacturer=Tablet
        S:  Product=XD-1212-U
        C:* #Ifs= 1 Cfg#= 1 Atr=80 MxPwr=140mA
        I:  If#= 0 Alt= 0 #EPs= 1 Cls=03(HID  ) Sub=01 Prot=02 Driver=wacom
        E:  Ad=81(I) Atr=03(Int.) MxPS=  10 Ivl=5ms

In this case, the tablet identifier is in Vendor=056a ProdID=0044. The
model (Product=XD-1212-U) is determined by ProdID; the Intuos2 12x12 is
0x44 for instance.

In the wacom.c (kernel 2.6.9/10) or wacom\_wac.c (kernels 2.6.11 or
later) file under linuxwacom/src/2.6.x, you will find a table called
"wacom\_ids". Look for your device identifier. Only the identifiers
listed are handled by the wacom driver, so if it is missing, it needs to
be added. Also look for routine usb\_hid\_configure() in hid-core.c if
you are running a kernel older than 2.6.18. At the beginning of the
routine, we added the following lines to let HID driver ignore all Wacom
devices:

            /* ignore all Wacom devices */
            if (dev->descriptor.idVendor == USB_VENDOR_ID_WACOM)
                return NULL;

If you've gotten this far, and still cannot get it to work, email me
with your device identifier and as much of an explanation of where
things did and did not work as described. I'll see what I can do about
at least finding out why it did not work. Then we can go on to
solutions.

The next section assumes you have things working up to this point.

Viewing the Raw Data
--------------------

See [Analysing kernel events](/wiki/Analysing_kernel_events "wikilink")
