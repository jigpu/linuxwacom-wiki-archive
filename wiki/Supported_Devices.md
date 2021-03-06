---
title: Supported Devices
permalink: wiki/Supported_Devices/
layout: wiki
---

Devices supported by the Linux Wacom Project are listed below. See the
[Device IDs](/wiki/Device_IDs "wikilink") page for additional detail.

<b>Serial devices:</b>

-   Intuos series\*
-   Cintiq series\*
-   TabletPC (with/without touch)

<b>USB series:</b>

-   Graphire series
-   Cintiq (V4 series)
-   CintiqPartner
-   Intuos 1, 2, 3, 4 & 5
-   Intuos and Intuos Pro
-   Cintiq (V5 series)
-   Volito series
-   PenPartner series
-   PL & DTF
-   Bamboo, Bamboo1, & BambooFun
-   Bamboo Pen & Touch
-   TabletPC (with/without touch)

<b>Bluetooth series:</b>

-   Graphire Bluetooth Wireless
-   Intuos4 Wireless

NOTE: Only ISDV4 serial devices (TabletPC's) are currently supported by
xf86-input-wacom (Xserver 1.7 or later). For legacy serial graphics
tablets a pair of serio kernel drivers are being developed, that in
conjunction with inputattach, provide support on xf86-input-wacom. They
are currently working or near completion. The protocol V tablet's
(Intuos and Instuos2) wacom\_serial5.ko is available
[here](https://github.com/RoaldFre/wacom_serial5).

NOTE: A FreeBSD Wacom driver is available through the
[FreeBSD](http://www.freshports.org/x11-drivers/input-wacom) ports
system. Please email your questions and/or suggestion directly to
[Bartosz Fabianowski](mailto:freebsd@chillt.de?subject=WACOM), the Wacom
FreeBSD developer.
