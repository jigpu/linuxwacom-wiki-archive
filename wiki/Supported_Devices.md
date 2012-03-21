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
xf86-input-wacom (Xserver 1.7 or later). For legacy serial graphics
tablets a pair of serio kernel drivers are being developed, that in
conjunction with inputattach, provide support on xf86-input-wacom. They
are both currently working and near completion and submission to the
kernel. The protocol IV tablet's (including the Digitizer II,
PenPartner, Graphire, and Cintiq tablets) latest wacom\_serial.ko is
available
[here](http://cipht.net/releases/wacom_serial-120301-1.tar.bz2). The
protocol V tablet's (Intuos and Instuos2) wacom\_serial5.ko is available
[here](https://github.com/RoaldFre/wacom_serial5).

NOTE: The Intuos 4 Wireless is not currently supported by the Bluez
stack. However patches for support are actively being developed. See the
[wacom-kernel](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/wacom-kernel;a=shortlog;h=refs/heads/devel)
repository and the [linuxwacom-devel
archive](https://sourceforge.net/mailarchive/forum.php?forum_name=linuxwacom-devel).
In the meantime you can use your usb "charging" cable. Basic support
included in kernel 3.3. Full functional support will be provided in
kernel 3.4.

NOTE: A FreeBSD Wacom driver is available through the
[FreeBSD](http://www.freshports.org/x11-drivers/input-wacom) ports
system. Please email your questions and/or suggestion directly to
[Bartosz Fabianowski](mailto:freebsd@chillt.de?subject=WACOM), the Wacom
FreeBSD developer.
