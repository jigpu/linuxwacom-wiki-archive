---
title: Wacomtablet
permalink: wiki/Wacomtablet/
layout: wiki
---

The OpenSolaris project used to have [a wiki
page](http://web.archive.org/web/20120829201957/http://hub.opensolaris.org/bin/view/Community+Group+device_drivers/wacomtablet)
that included a set of patches to get our X11 driver working with their
STREAMS kernel module (ported from the FreeBSD uwacom driver). With the
demise of OpenSolaris, this wiki was taken offline, rendering the
patches inaccessible. While this project's primary concern is supporting
the Linux operating system, it is a disservice to users of
OpenSolaris/Illumos to have these patches simply disappear. To remedy
this, 'solaris' branches for these patches have added to both the
[linuxwacom](linuxwacom "wikilink") and
[xf86-input-wacom](xf86-input-wacom "wikilink") repositories.

**Status**

-   Kernel STREAMS module was integrated into snv\_132.
-   X.org Wacom Tablet driver and tools (see <http://linuxwacom.sf.net>
    )
    -   v0.8.4: works on OpenSolaris snv\_115 - snv\_129.
    -   v0.10.4: works on OpenSolaris snv\_130.

**Documents**

-   [PSARC/2009/621 usbwcm: Wacom USB Tablet STREAMS
    module](http://www.mail-archive.com/opensolaris-arc@mail.opensolaris.org/msg18387.html)

**Source code and patches**

-   kernel STREAMS module: usbwcm
-   X.org Wacom Tablet driver and tools
    -   v0.8.4:
        [source](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/linuxwacom;a=shortlog;h=refs/heads/solaris)
    -   v0.10.4:
        [source](http://linuxwacom.git.sourceforge.net/git/gitweb.cgi?p=linuxwacom/xf86-input-wacom;a=shortlog;h=refs/heads/solaris)

**Known Problems**

-   OpenSolaris builds before snv\_115:
    -   Not tested.
    -   HAL input device support was not integrated until snv\_115.
    -   The Wacom X.org XInput driver v0.8.4-4 may comiple with our
        patch, but Wacom USB tablets will not be listed in HAL device
        list. You need to edit xorg.conf to make your tablets working.
-   OpenSolaris builds **since snv\_130**:
    -   Please use X.org Wacom Tablet driver and tools v0.10.4.
    -   We will contribute our modifications for Solaris porting back
        into the upstream.

**References**

-   Wacom website: [1](http://www.wacom.com)
-   FreeBSD uwacom driver
    [2](http://www.freshports.org/x11-drivers/input-wacom)

**Supported Hardware and Platform**

-   Devices supported
    -   Wacom Graphire3
    -   Wacom Graphire4
    -   Wacom Bamboo
    -   Wacom Bamboo Fun
    -   Wacom Intuos3
    -   Wacom Intuos4
    -   Wacom Cintiq 21UX

<!-- -->

-   Tested platforms
    -   x86 and SPARC
