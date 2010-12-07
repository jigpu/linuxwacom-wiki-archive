---
title: DeveloperPages
permalink: wiki/DeveloperPages/
layout: wiki
---

For Wacom kernel driver development, please submit your patches to the
[Linux Kernel Developers Mailing List](http://lkml.org). Only patches
that appear in the upstream kernel will be appear in distributions and
thus ensure tablets will be working out-of-the-box with future releases
of your distribution.

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

Making releases
===============

This section describes how to do a new xf86-input-wacom release. VERSION
stands for the new version (e.g. 0.10.3), OLDVERSION for the previous
version (e.g. 0.10.2).

1.  Change configure.ac to reflect the new version number and commit:
    **git commit -sm "wacom VERSION" configure.ac** You *must not* have
    uncommitted changes when releasing a new version.
2.  Run **autoreconf -iv** or **autogen.sh**, build and install the
    driver. Verify that the new version shows in the Xorg.log.
3.  Run **make distcheck**. Verify the name of the tarballs
    (xf86-input-wacom-VERSION.tar.bz2 and
    xf86-input-wacom-VERSION.tar.gz). If make distcheck fails, reset
    with **git reset --hard HEAD~1** and resolve the bugs first.
4.  Tag the module: '''git tag -m "xf86-input-wacom VERSION" -s
    xf86-input-wacom-VERSION
5.  Push to the remote: **git push origin master**
6.  Run the release script: **sh release.sh**. This script will push the
    tag and upload the tarballs to the remote.
7.  Type up a short announce email summarising the new features in this
    version and append the git shortlog: **git shortlog
    xf86-input-wacom-OLDVERSION..xf86-input-wacom-VERSION**
8.  **Sign off the email** and send it to the [ linuxwacom-announce
    list](/wiki/Mailing_lists "wikilink")

Note that it may take a while until the uploaded tarballs are available
through the download links.

Device IDs
==========

| Protocol 4 series (no serial numbers are reported) |                                                                                                                     |
|----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| Earlier Graphire without pad                       | 0x00, 0x10, 0x11, 0x12, 0x13, 0x14                                                                                  |
| Graphire 4                                         | 0x15, 0x16                                                                                                          |
| Bamboo Fun                                         | 0x17, 0x18, 0x65                                                                                                    |
| Bamboo1 and LLC (low low cost)                     | 0x19, 0x60, 0x61, 0x62, 0x63, 0x64, 0x69                                                                            |
| New LLC Bamboo similiar to Earlier Graphires       | 0xD4                                                                                                                |
| PL series                                          | 0x30, 0x31, 0x32, 0x33, 0x34, 0x35, 0x37, 0x38, 0x39, 0xC4, 0xC0, 0xC2, 0xC7                                        |
| Cap+                                               | 0x9F                                                                                                                |
| PenPartner                                         | 0x03                                                                                                                |
| Bamboo 2FGT and penabled                           | 0xD1, 0xD2, 0xD3                                                                                                    |
| Bamboo 2FGT only                                   | 0xD0                                                                                                                |
| TabletPC (penabled and no touch, no mouse)         | 0x90                                                                                                                |
| TabletPC (penabled with 1FGT, no mouse)            | 0x93, 0wx9A                                                                                                         |
| TabletPC ( penabled with 2FGT)                     | 0xE2                                                                                                                |
| TabletPC (2FGT only)                               | 0xE3                                                                                                                |
| Protocol 5 series                                  |                                                                                                                     |
| Intuos                                             | 0x20, 0x21, 0x22, 0x23, 0x24                                                                                        |
| Intuos2                                            | 0x41, 0x42, 0x43, 0x44, 0x45                                                                                        |
| Intuos3                                            | 0xB0, 0xB1, 0xB2, 0xB3, 0xB4, 0xB5, 0xB7, 0xB8,                                                                     |
| Intuos4                                            | 0xB9, 0xBA, 0xBB,                                                                                                   |
| Cintiq 21UX                                        | 0x3F                                                                                                                |
| Cintiq 12SWX and 20SWX                             | 0xC5, 0xC6,                                                                                                         |
| ISDv4 series                                       |                                                                                                                     |
| WACf000 - WACf007                                  | penabled (IDs translated into 0x90)                                                                                 |
| WACf008 - WACf00A                                  | penabled with 1FGT (IDs translated into 0x93)                                                                       |
| WACf00B - WACf00E                                  | penabled with 2FGT (IDs translated into 0xE2)                                                                       |
| WACf010                                            | 2FGT only (IDs translated into 0xE3)                                                                                |
| FUJ02E5                                            | penabled (IDs tranlated into 0x90. Not truly supported. It falls into our default and it works :)                   |
| FUJ02E7                                            | penabled with 2FGT (IDs translated into 0xE2. Not truly supported. It falls into our default and it almost works :) |
| FUJ02E9                                            | penabled with 1FGT (IDs translated into 0x93. Not truly supported. It falls into our default and it almost works :) |
|                                                    |                                                                                                                     |
