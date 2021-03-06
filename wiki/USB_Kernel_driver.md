---
title: USB Kernel driver
permalink: wiki/USB_Kernel_driver/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

Wacom devices need a specific kernel driver to claim the tablet. The
standard USB HID driver will not expose all features of the tablets and
thus the tablet will not work.

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
kernel and the kernel driver/module wacom.ko needs to be updated. See
below.

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
update the kernel driver. Check if your distribution offers an updated
wacom.ko through kernel module backports from upstream kernels or other
distribution package. For example Ubuntu users could try [Martin Owens'
PPA](https://launchpad.net/~doctormo/+archive/wacom-plus). If no newer
wacom.ko is available in your distribution see
[input-wacom](input-wacom "wikilink").

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

To check the event stream coming from the kernel, see [Analysing kernel
events](/wiki/Analysing_kernel_events "wikilink")
