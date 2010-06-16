---
title: USB Kernel driver
permalink: wiki/USB_Kernel_driver/
layout: wiki
---

The USB Kernel Driver
=====================

Serial tablet users rejoice: you can skip this entire section. Please go
to the [Viewing Wacom Data](/wiki/Viewing_Wacom_Data "wikilink") (wacdump)
page for details on viewing the tablet output. USB users stay put; we
need to tweak your kernel.

Kernel modules must be recompiled for each new kernel so I can't just
provide binaries. By the time you read this, my present kernel will be
entirely out of date with yours.

In any event, many new features are available in the latest drivers from
the Linux Wacom Project, so I wholly recommend using them over the
drivers provided by your standard distribution. Rest assured, continuous
efforts are being made to get these changes merged back into the Linux
kernel. However, the changes can normally only merged into the next
kernel release, instead of the current one.

For those who don't like upgrading kernels, here is a safe statement: if
you are not using a newly released tablet model and you are running a
recently released kernel version, chances are that you don't need to
update your kernel driver from linuxwacom.

Note: If your system is running on a 2.6.9 or older kernel and you want
to use a new Wacom device, upgrading to a newer version (preferablely
2.6.18 or later) is required since we do not support kernels older than
2.6.9 any more.

Testing Tablet Detection
------------------------

In this section we will determine which driver, if any, claims control
over the tablet. There are at least two drivers that are interested:

`# (usb)hid.o which may think it is an HID device (for kernel 2.6.17 or older),`  
`# the wacom driver which should identify the tablet as its own.`

To see which driver is driving the tablet, issuing more
/proc/bus/usb/devices should list something similiar to the following:

        [jej@ayukawa wacom]$more /proc/bus/usb/devices
        T:  Bus=01 Lev=01 Prnt=01 Port=01 Cnt=02 Dev#=  3 Spd=12  MxCh= 0
        D:  Ver= 1.10 Cls=00(>ifc ) Sub=00 Prot=00 MxPS= 8 #Cfgs=  1
        P:  Vendor=056a ProdID=0042 Rev= 1.15
        S:  Manufacturer=Tablet
        S:  Product=XD-0608-U
        C:* #Ifs= 1 Cfg#= 1 Atr=80 MxPwr=140mA
        I:  If#= 0 Alt= 0 #EPs= 1 Cls=03(HID  ) Sub=01 Prot=02 Driver=wacom
        E:  Ad=81(I) Atr=03(Int.) MxPS=  10 Ivl=5ms

where Vendor=056a indicates a Wacom device. Driver=wacom means Wacom
driver is in control of the tablet. If you see anything other than wacom
after Driver=, at least hid-core.c needs to be updated.

On newer 2.6 systems, more /proc/bus/input/devices gives you

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

where, again, Vendor=056a indicates a Wacom device. Name="Wacom Intuos2
6x8" means an Intuos2 6x8 tablet reported to /dev/input/event3. If there
is no Wacom after Name=, you need to update wacom.c.

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
like pressure and tilt will be received on /dev/input/event3. You are
ready to configure the X driver Downloading the Code.

If instead you got any of the following lines in your log, the wacom
driver did not get control. Either your wacom driver doesn't support
your tablet or hid is in control.

            input0,hiddev0: USB HID v1.00 Mouse [Tablet PTK-640] on usb1:5.0
            input0: Tablet PTK-640 on usb1:5.0

Updated wacom.c
---------------

The wacom kernel driver (wacom\_wac.c and wacom\_sys.c) that is
available in linuxwacom-0.8.6-1.tar.bz2 supports Tablet PCs (0xE2 and
0xE3), which will be available in kernel version 2.6.34.

The USB Wacom tablet may be assigned to a different /dev/input/event\#
after each reboot. To resolve this issue, you can link Wacom USB tablet
to "/dev/input/wacom". It can be done by adding the following rules in
/etc/udev/rules.d/60-wacom.rules. Some distributions use a different
number for the file name. Please look for proper one with wacom under
/etc/udev/rules.d.

Below are examples from Debian GNU/Linux distribution:

60-wacom.rules on systems using ATTRS

Systems with only one Wacom device:

        # udev rules for wacom tablets.

        KERNEL!="event[0-9]*", GOTO="wacom_end"

        # Multiple interface support for stylus and touch devices.
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        # Convenience links for the common case of a single tablet.  We could do just this:
        #ATTRS{idVendor}=="056a", SYMLINK+="input/wacom-$env{WACOM_TYPE}"
        # but for legacy reasons, we keep the input/wacom link as the generic stylus device.
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

        # Check and repossess the device if a module other than the wacom one
        # is already bound to it.
        ATTRS{idVendor}=="056a", ACTION=="add", RUN+="check_driver wacom $devpath $env{ID_BUS}"

        LABEL="wacom_end"

Systems with more than one Wacom devices:

        # udev rules for wacom tablets.
        # These rules were compiled for the Debian GNU/Linux distribution,
        # but others may, and indeed are encouraged to, use them also.
        #
        # Should you do so, PLEASE CO-ORDINATE ANY CHANGES OR ADDITIONS
        # of new devices with Ron  so that we can try
        # to present users with a standard set of device nodes
        # which they can rely on across the board.

        KERNEL!="event[0-9]*", GOTO="wacom_end"

        # Port specific link for users of multiple tablets of the same type.
        # The ID_PATH variable is set by the "path_id" script in an earlier rule file.
        ATTRS{idVendor}=="056a", ENV{ID_PATH}=="?*", SYMLINK="input/by-path/$env{ID_PATH}-wacom"

        # Multiple interface support for stylus and touch devices.
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        # Type-named links for multiple tablets.  If you want to use multiple
        # tablets of the _same_ type, you will probably need to use the links
        # from /dev/input/by-path to identify which is plugged into what usb
        # port.  For different tablet types though, just pick your links from
        # the list below.
        #
        # We override SYMLINK for tabletpc devices because the by-path link
        # is not required with such devices, there will only ever be one.
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0000", SYMLINK+="input/tablet-penpartner"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0003", SYMLINK+="input/tablet-cintiq_partner"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0010", SYMLINK+="input/tablet-graphire"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0011", SYMLINK+="input/tablet-graphire2-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0012", SYMLINK+="input/tablet-graphire2-5x7"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0013", SYMLINK+="input/tablet-graphire3"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0014", SYMLINK+="input/tablet-graphire3-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0015", SYMLINK+="input/tablet-graphire4-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0016", SYMLINK+="input/tablet-graphire4-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0017", SYMLINK+="input/tablet-bamboofun-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0018", SYMLINK+="input/tablet-bamboofun-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0019", SYMLINK+="input/tablet-bamboo1-medium"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0020", SYMLINK+="input/tablet-intuos-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0021", SYMLINK+="input/tablet-intuos-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0022", SYMLINK+="input/tablet-intuos-9x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0023", SYMLINK+="input/tablet-intuos-12x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0024", SYMLINK+="input/tablet-intuos-12x18"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0030", SYMLINK+="input/tablet-pl400"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0031", SYMLINK+="input/tablet-pl500"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0032", SYMLINK+="input/tablet-pl600"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0033", SYMLINK+="input/tablet-pl600sx"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0034", SYMLINK+="input/tablet-pl550"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0035", SYMLINK+="input/tablet-pl800"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0037", SYMLINK+="input/tablet-pl700"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0038", SYMLINK+="input/tablet-pl510"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0039", SYMLINK+="input/tablet-dtu710"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="003f", SYMLINK+="input/tablet-cintiq21ux"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0041", SYMLINK+="input/tablet-intuos2-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0042", SYMLINK+="input/tablet-intuos2-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0043", SYMLINK+="input/tablet-intuos2-9x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0044", SYMLINK+="input/tablet-intuos2-12x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0045", SYMLINK+="input/tablet-intuos2-12x18"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0047", SYMLINK+="input/tablet-intuos2-6x8a"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0060", SYMLINK+="input/tablet-volito"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0061", SYMLINK+="input/tablet-penstation2"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0062", SYMLINK+="input/tablet-volito2-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0063", SYMLINK+="input/tablet-volito2-2x3"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0064", SYMLINK+="input/tablet-penpartner2"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0065", SYMLINK+="input/tablet-bamboo"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0069", SYMLINK+="input/tablet-bamboo1"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0081", SYMLINK+="input/tablet-graphire_bt-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0090", SYMLINK="input/tablet-tpc90"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0093", SYMLINK="input/tablet-tpc93-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="009a", SYMLINK="input/tablet-tpc9a-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b0", SYMLINK+="input/tablet-intuos3-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b1", SYMLINK+="input/tablet-intuos3-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b2", SYMLINK+="input/tablet-intuos3-9x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b3", SYMLINK+="input/tablet-intuos3-12x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b4", SYMLINK+="input/tablet-intuos3-12x19"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b5", SYMLINK+="input/tablet-intuos3-6x11"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b7", SYMLINK+="input/tablet-intuos3-4x6"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b8", SYMLINK+="input/tablet-intuos4-4x6"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b9", SYMLINK+="input/tablet-intuos4-6x9"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00ba", SYMLINK+="input/tablet-intuos4-8x13"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00bb", SYMLINK+="input/tablet-intuos4-12x19"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c0", SYMLINK+="input/tablet-dtf521"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c4", SYMLINK+="input/tablet-dtf720"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c5", SYMLINK+="input/tablet-cintiq20wsx"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c6", SYMLINK+="input/tablet-cintiq12wx"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c7", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/tablet-dtu1931"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d0", SYMLINK="input/tablet-bamboo2fg-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d1", SYMLINK="input/tablet-bamboo2fg-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d2", SYMLINK="input/tablet-bamboo-craft-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d3", SYMLINK="input/tablet-bamboo2fg-6x8-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d4", SYMLINK="input/tablet-bamboo-4x5-$env{WACOM_TYPE}"
         
        # Convenience links for the common case of a single tablet.  We could do just this:
        #ATTRS{idVendor}=="056a", SYMLINK+="input/wacom-$env{WACOM_TYPE}"
        # but for legacy reasons, we keep the input/wacom link as the generic stylus device.
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

        # Check and repossess the device if a module other than the wacom one
        # is already bound to it.
        ATTRS{idVendor}=="056a", ACTION=="add", RUN+="check_driver wacom $devpath $env{ID_BUS}"

        LABEL="wacom_end"

60-wacom.rules on systems using SYSFS

Systems with only one Wacom device:

        # udev rules for wacom devices
        # will create:
        #   /dev/input/wacom
        #   /dev/input/wacom-touch
        BUS=="usb", KERNEL=="event*", SYSFS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        BUS=="usb", KERNEL=="event*", SYSFS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

Systems with more than one Wacom devices:

        # udev rules for wacom devices
        # will create:
        #   /dev/input/wacom
        #   /dev/input/wacom-tablets/
        #   /dev/input/wacom-tablets/-
        #
        # The last rule is intended for cases when you have more than one tablet
        # of the same model. The id is usb port dependent.
        #
        BUS=="usb", KERNEL=="event*", SYSFS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        BUS=="usb", KERNEL=="event*", SYSFS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        # Type-named links for multiple tablets.  If you want to use multiple
        # tablets of the _same_ type, you will probably need to use the links
        # from /dev/input/wacom-tablets to identify which is plugged into what usb
        # port.  For different tablet types though, just pick your links from
        # the list below.
        #
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0000", SYMLINK+="input/wacom-tablets/penpartner input/wacom-tablets/penpartner-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0010", SYMLINK+="input/wacom-tablets/graphire input/wacom-tablets/graphire-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0011", SYMLINK+="input/wacom-tablets/graphire2-4x5 input/wacom-tablets/graphire2-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0012", SYMLINK+="input/wacom-tablets/graphire2-5x7 input/wacom-tablets/graphire2-5x7-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0013", SYMLINK+="input/wacom-tablets/graphire3 input/wacom-tablets/graphire3-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0014", SYMLINK+="input/wacom-tablets/graphire3-6x8 input/wacom-tablets/graphire3-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0015", SYMLINK+="input/wacom-tablets/graphire4-4x5 input/wacom-tablets/graphire4-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0016", SYMLINK+="input/wacom-tablets/graphire4-6x8 input/wacom-tablets/graphire4-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0017", SYMLINK+="input/wacom-tablets/bamboofun-4x5 input/wacom-tablets/bamboofun-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0018", SYMLINK+="input/wacom-tablet/bamboofun-6x8 input/wacom-tablets/bamboofun-6x8"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0019", SYMLINK+="input/wacom-tablet/bamboo1-medium input/wacom-tablets/bamboo1-medium"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0060", SYMLINK+="input/wacom-tablets/volito input/wacom-tablets/volito-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0061", SYMLINK+="input/wacom-tablets/penstation2 input/wacom-tablets/penstation2-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0062", SYMLINK+="input/wacom-tablets/volito2-4x5 input/wacom-tablets/volito2-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0063", SYMLINK+="input/wacom-tablets/volito2-2x3 input/wacom-tablets/volito2-2x3-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0064", SYMLINK+="input/wacom-tablets/penpartner2 input/wacom-tablets/penpartner2-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0020", SYMLINK+="input/wacom-tablets/intuos-4x5 input/wacom-tablets/intuos-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0021", SYMLINK+="input/wacom-tablets/intuos-6x8 input/wacom-tablets/intuos-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0022", SYMLINK+="input/wacom-tablets/intuos-9x12 input/wacom-tablets/intuos-9x12-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0023", SYMLINK+="input/wacom-tablets/intuos-12x12 input/wacom-tablets/intuos-12x12-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0024", SYMLINK+="input/wacom-tablets/intuos-12x18 input/wacom-tablets/intuos-12x18-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0030", SYMLINK+="input/wacom-tablets/pl400 input/wacom-tablets/pl400-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0031", SYMLINK+="input/wacom-tablets/pl500 input/wacom-tablets/pl500-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0032", SYMLINK+="input/wacom-tablets/pl600 input/wacom-tablets/pl600-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0033", SYMLINK+="input/wacom-tablets/pl600sx input/wacom-tablets/pl600sx-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0034", SYMLINK+="input/wacom-tablets/pl550 input/wacom-tablets/pl550-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0035", SYMLINK+="input/wacom-tablets/pl800 input/wacom-tablets/pl800-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0037", SYMLINK+="input/wacom-tablets/pl700 input/wacom-tablets/pl700-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0038", SYMLINK+="input/wacom-tablets/pl510 input/wacom-tablets/pl510-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0039", SYMLINK+="input/wacom-tablets/dtu710 input/wacom-tablets/dtu710-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00c0", SYMLINK+="input/wacom-tablets/dtf521 input/wacom-tablets/dtf521-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00c4", SYMLINK+="input/wacom-tablets/dtf720 input/wacom-tablets/dtf720-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0003", SYMLINK+="input/wacom-tablets/cintiq_partner input/wacom-tablets/cintiq_partner-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0041", SYMLINK+="input/wacom-tablets/intuos2-4x5 input/wacom-tablets/intuos2-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0042", SYMLINK+="input/wacom-tablets/intuos2-6x8 input/wacom-tablets/intuos2-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0043", SYMLINK+="input/wacom-tablets/intuos2-9x12 input/wacom-tablets/intuos2-9x12-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0044", SYMLINK+="input/wacom-tablets/intuos2-12x12 input/wacom-tablets/intuos2-12x12-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0045", SYMLINK+="input/wacom-tablets/intuos2-12x18 input/wacom-tablets/intuos2-12x18-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b0", SYMLINK+="input/wacom-tablets/intuos3-4x5 input/wacom-tablets/intuos3-4x5-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b1", SYMLINK+="input/wacom-tablets/intuos3-6x8 input/wacom-tablets/intuos3-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b2", SYMLINK+="input/wacom-tablets/intuos3-9x12 input/wacom-tablets/intuos3-9x12-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b3", SYMLINK+="input/wacom-tablets/intuos3-12x12 input/wacom-tablets/intuos3-12x12-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b4", SYMLINK+="input/wacom-tablets/intuos3-12x19 input/wacom-tablets/intuos3-12x19-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b5", SYMLINK+="input/wacom-tablets/intuos3-6x11 input/wacom-tablets/intuos3-6x11-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="003f", SYMLINK+="input/wacom-tablets/cintiq21ux input/wacom-tablets/cintiq21ux-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0047", SYMLINK+="input/wacom-tablets/intuos2-6x8a input/wacom-tablets/intuos2-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b7", SYMLINK+="input/wacom-tablets/intuos3-4x6 input/wacom-tablets/intuos3-4x6-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b8", SYMLINK+="input/wacom-tablets/intuos4-4x6 input/wacom-tablets/intuos4-4x6-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00b9", SYMLINK+="input/wacom-tablets/intuos4-6x9 input/wacom-tablets/intuos4-6x9-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00ba", SYMLINK+="input/wacom-tablets/intuos4-8x13 input/wacom-tablets/intuos4-8x13-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00bb", SYMLINK+="input/wacom-tablets/intuos4-12x19 input/wacom-tablets/intuos4-12x19-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0065", SYMLINK+="input/wacom-tablets/bamboo input/wacom-tablets/bamboo-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00c6", SYMLINK+="input/wacom-tablets/cintiq12wx input/wacom-tablets/cintiq12wx-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00c5", SYMLINK+="input/wacom-tablets/cintiq20wsx input/wacom-tablets/cintiq20wsx-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00c7", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom-tablets/dtu1931 input/wacom-tablets/dtu1931-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0069", SYMLINK+="input/wacom-tablets/bamboo1 input/wacom-tablets/bamboo1-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0081", SYMLINK+="input/wacom-tablets/graphire_bt-6x8 input/wacom-tablets/graphire_bt-6x8-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0090", SYMLINK="input/wacom-tablets/tpc90"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="0093", SYMLINK="input/wacom-tablets/tpc93-$env{WACOM_TYPE}"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="009a", SYMLINK="input/wacom-tablets/tpc9a-$env{WACOM_TYPE}"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00d0", SYMLINK="input/tablet-bamboo2fg-$env{WACOM_TYPE}-%b"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00d1", SYMLINK="input/tablet-bamboo2fg-4x5-$env{WACOM_TYPE}"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00d2", SYMLINK="input/tablet-bamboo-craft-$env{WACOM_TYPE}"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00d3", SYMLINK="input/tablet-bamboo2fg-6x8-$env{WACOM_TYPE}"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", SYSFS{idProduct}=="00d4", SYMLINK="input/tablet-bamboo-4x5-$env{WACOM_TYPE}"

        # Convenience links for the common case of a single tablet.  We could do just this:
        #SYSFS{idVendor}=="056a", SYMLINK+="input/wacom-$env{WACOM_TYPE}"
        # but for legacy reasons, we keep the input/wacom link as the generic stylus device.
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        BUS=="usb", KERNEL=="event*", SYSFS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

Newer Mandriva Linux (Mandriva 2007 Spring and later) has an application
called mousedrake which takes care of the setup and configuration of
linuxwacom driver. If you use Mandriva Linux and you see InputDevice
sections for Wacom device in your Xorg.conf, chances are your Wacom
tablet is ready for you to draw.

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

Viewing the Raw Data (xxd)
--------------------------

View the raw data from the tablet, by returning to the /dev/input
directory and streaming the data directly from the device. Be patient
here because this is where a lot of people are getting stuck.

        [root@ayukawa usb]# cd /dev/input
        [root@ayukawa input]# xxd event0
        0000000: e65d c33d 597d 0100 0100 4101 0100 0000  .].=Y}....A.....
        0000010: e65d c33d 5c7d 0100 0400 0000 b701 2800  .].=\}........(.
        0000020: e65d c33d d9bb 0100 0100 4101 0000 0000  .].=......A.....
        0000030: e65d c33d dcbb 0100 0400 0000 b701 2800  .].=..........(.
        (Ctrl-C)

First off, you have to move the mouse or tap the pen to get any output.
If the tablet is mapped to event0, a continuously data stream will be
displayed while you move the mouse or pen on the tablet. Second, you
might not get anything at all. Don't panic. This seems to happen
occasionally. If absolutely no output occurs, try event1 and event2. If
no output occurs on those ports, reload the drive:

        [jej@sasami root]# /sbin/rmmod wacom
        [jej@sasami root]# /sbin/modprobe wacom       (or /sbin/insmod mydir/src/wacom.o)
        [jej@sasami root]# tail /var/log/messages
        May 04 17:31:31 sasami kernel: usb.c: deregistering driver wacom
        May 04 17:31:34 sasami kernel: usb.c: registered new driver wacom
        May 04 17:31:35 sasami kernel: input0: Wacom Intuos2 12x12 on usb1:2.0
        May 04 17:31:35 sasami kernel: wacom.c: v1.43-0.8.6-1 Vojtech Pavlik <vojtech@suse.cz>

The device driver and the tablet occassionally get out of sync with the
tablet thinking it's still in HID mode when in fact it should be in
"mode 2." By unloading and reloading the driver manually, the
initialization code has another opportunity to get it right. Try the xxd
/dev/input/event0 again. This time, it should work. If not, send me some
email.

Incidentally, if you have a program running that is connected to
/dev/input/event0 (like X or wacdump for instance), it is possible that
the tablet will not reattach back to the same event. I have seen the
wacom reattach to /dev/input/event1 when unloading and reloading the
wacom driver with wacdump running for instance. So, try xxd on event1 or
event2 if event0 fails.

You should also try running xxd on /dev/input/mouse0. You should get
streams of data when the mouse and pen are moved around the surface of
the tablet. It is this device that X will look at for mouse movement.
Use Ctrl-C to exit xxd.