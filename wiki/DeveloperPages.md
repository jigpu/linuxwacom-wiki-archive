---
title: DeveloperPages
permalink: wiki/DeveloperPages/
layout: wiki
---

For working tablet support, you need two vital pieces installed on your
machine. A kernel driver that recognises the tablet and understands the
hardware protocol, and an X driver that allows X to use the device.

X driver development
====================

[xf86-input-wacom](xf86-input-wacom "wikilink") is in a git repository.
To develop, the following steps are required:

-   clone the source

` git clone `[`git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom`](git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom)

-   build the source

` ./autogen.sh --prefix=/usr`

-   hack it!
-   create a patch with **git format-patch**
-   submit the patch to the [linuxwacom-devel mailing
    list](https://lists.sourceforge.net/lists/listinfo/linuxwacom-devel)

For more information on these steps, see

-   [Using Git](/wiki/Using_Git "wikilink")
-   [Installing from source](/wiki/Installing_from_source "wikilink")
-   [Submitting Patches](/wiki/Submitting_Patches "wikilink")

Developing for linuxwacom
-------------------------

linuxwacom is a collection of backports for both older kernels and older
X servers. The linuxwacom repository is maintained in CVS and releases
are made regularly. If you want to propose a patch, please follow the
instructions [ here](/wiki/LinuxwacomDevelopment "wikilink")

Kernel driver development
=========================

For Wacom kernel driver development, please submit your patches to the
[Linux Kernel Developers Mailing List](http://lkml.org). Only patches
that appear in the upstream kernel will be appear in distributions and
thus ensure tablets will be working out-of-the-box with future releases
of your distribution.

**FIXME** more information needed here.

Note that kernel patches integrated into the
[linuxwacom](linuxwacom "wikilink") tarball usually do not find their
way upstream and thus need to be re-implemented or forward-ported for
future kernels. Additionally, kernel patches in linuxwacom are not
shipped by any major distribution. Any kernel developer is encouraged to
work on the [upstream kernel](http://kernel.org) instead.

Kernel Data
===========

[Intuos4 Kernel Data stream](/wiki/Intuos4_Kernel_Data_stream "wikilink")

[Kernel Input Event Overview](/wiki/Kernel_Input_Event_Overview "wikilink")

Maintainer information
======================

-   [Making Releases](/wiki/Making_Releases "wikilink"): information on how to
    cut a new release for
    [xf86-input-wacom](xf86-input-wacom "wikilink").

Device IDs
==========

| Protocol 4 series (no serial numbers are reported) |                                                                                                                     |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| Earlier Graphire without pad                       | 0x00, 0x10, 0x11, 0x12, 0x13, 0x14                                                                                  |
| Graphire 4                                         | 0x15, 0x16, 0x81                                                                                                    |
| Bamboo Fun                                         | 0x17, 0x18, 0x65                                                                                                    |
| Bamboo1 and LLC (low low cost)                     | 0x19, 0x60, 0x61, 0x62, 0x63, 0x64, 0x69                                                                            |
| PL series                                          | 0x30, 0x31, 0x32, 0x33, 0x34, 0x35, 0x37, 0x38, 0x39, 0xC4, 0xC0, 0xC2, 0xC7                                        |
| Cap+                                               | 0x9F                                                                                                                |
| PenPartner                                         | 0x03                                                                                                                |
| Bamboo Pen (LLC similar to Earlier Graphire)       | 0xD4                                                                                                                |
| Bamboo Touch (2FGT only)                           | 0xD0                                                                                                                |
| Bamboo P & T (penabled with 2FGT or 4FGT)          | 0xD1, 0xD2, 0xD3, 0xD6, 0xD7, 0xD8, 0xDA, 0xDB. 3&4FGT for 0xDA & 0xDB not supported.                               |
| TabletPC (penabled and no touch, no mouse)         | 0x90                                                                                                                |
| TabletPC (penabled with 1FGT, no mouse)            | 0x93, 0x9A                                                                                                          |
| TabletPC ( penabled with 2FGT)                     | 0xE2                                                                                                                |
| TabletPC (2FGT only)                               | 0xE3                                                                                                                |
| Protocol 5 series                                  |                                                                                                                     |
| Intuos                                             | 0x20, 0x21, 0x22, 0x23, 0x24                                                                                        |
| Intuos2                                            | 0x41, 0x42, 0x43, 0x44, 0x45                                                                                        |
| Intuos3                                            | 0xB0, 0xB1, 0xB2, 0xB3, 0xB4, 0xB5, 0xB7                                                                            |
| Intuos4                                            | 0xB8, 0xB9, 0xBA, 0xBB, 0xBC, 0xBD                                                                                  |
| Cintiq 21UX                                        | 0x3F                                                                                                                |
| Cintiq 12SWX and 20SWX                             | 0xC5, 0xC6,                                                                                                         |
| Cintiq 21UX2                                       | 0xCC                                                                                                                |
| ISDv4 series                                       |                                                                                                                     |
| WACf000 - WACf007                                  | penabled (IDs translated into 0x90)                                                                                 |
| WACf008 - WACf00A                                  | penabled with 1FGT (IDs translated into 0x93)                                                                       |
| WACf00B - WACf00E                                  | penabled with 2FGT (IDs translated into 0xE2)                                                                       |
| WACf010                                            | 2FGT only (IDs translated into 0xE3)                                                                                |
| FUJ02E5                                            | penabled (IDs tranlated into 0x90. Not truly supported. It falls into our default and it works :)                   |
| FUJ02E7                                            | penabled with 2FGT (IDs translated into 0xE2. Not truly supported. It falls into our default and it almost works :) |
| FUJ02E9                                            | penabled with 1FGT (IDs translated into 0x93. Not truly supported. It falls into our default and it almost works :) |
|                                                    |                                                                                                                     |
