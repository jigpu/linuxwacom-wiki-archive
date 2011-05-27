---
title: Supported Devices
permalink: wiki/Supported_Devices/
layout: wiki
---

Devices supported by the Linux Wacom Project are listed below. See the
[Device IDs](/wiki/Device_IDs "wikilink") page for additional detail.

<b>Serial devices:</b>

-   UD series & PenPartner\*
-   Graphire & Intuos series\*
-   Cintiq series\*
-   TabletPC (with/without touch)

<b>USB series:</b>

-   Graphire series
-   Cintiq (V4 series)
-   CintiqPartner
-   Intuos 1, 2, 3, & 4
-   Cintiq (V5 series)
-   Volito series
-   PenPartner series
-   PL & DTF
-   Bamboo, Bamboo1, & BambooFun
-   Bamboo Pen & Touch
-   TabletPC (with/without touch)

<b>Bluetooth series:</b>

-   Graphire Bluetooth Wireless
-   Intuos4 Wireless\*

NOTE: Only ISDV4 serial devices (TabletPC's) are currently supported by
xf86-input-wacom (Xserver 1.7 or later). A [patch
set](http://sourceforge.net/mailarchive/forum.php?thread_name=4BEB198B.9030809%40sleif.de&forum_name=linuxwacom-discuss)
by John Tsiombikas & Sebastian Berthold for the serial tablets needs
further development before inclusion into xf86-input-wacom.

NOTE: Intuos 4 Wireless is not currently supported by the Bluez stack.
It is not simply a matter of adding the Vendor ID 0x56a and Product ID
0xBD alongside the Graphire Bluetooth Wireless' 0x56a & 0x81. This is
because the Graphire Bluetooth Wireless is a Protocol 4 device (tool
serial numbers are not reported) while the Intuos 4 Wireless is Protocol
5 (serial numbers reported). So code for Protocol 5 support needs to be
added to the Bluez stack. In the meantime you will need to use your usb
"charging" cable for your tablet to work in linux.

NOTE: A FreeBSD Wacom driver is available through the
[FreeBSD](http://www.freshports.org/x11-drivers/input-wacom) ports
system. Please email your questions and/or suggestion directly to
[Bartosz Fabianowski](mailto:freebsd@chillt.de?subject=WACOM), the Wacom
FreeBSD developer.
