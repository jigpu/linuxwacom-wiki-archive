---
title: Main Page
permalink: wiki/Main_Page/
layout: wiki
---

The Linux Wacom Project
=======================

The Linux Wacom Project manages the drivers, libraries, and
documentation for configuring and running Wacom tablets under the Linux
operating system. It contains diagnostic applications as well as updated
kernel drivers and
[XFree86](http://www.xfree86.org)/[X.Org](http://www.x.org) X input
drivers. Currently, the Linux Wacom Project provides two packages:

-   [xf86-input-wacom](xf86-input-wacom "wikilink"): the X11 input
    driver for X Servers 1.7 and later
    ([X11R7.5](http://who-t.blogspot.com/2009/10/x11r75-released-but-what-is-it.html))
-   [linuxwacom](linuxwacom "wikilink"): a combined package of kernel
    patches (backports!) and the X11 input driver for X Servers up to
    including 1.6.

Please read [Wacom Support in
Linux](http://who-t.blogspot.com/2010/09/wacom-support-in-linux.html)
first for a better understanding of the difference between
[linuxwacom](linuxwacom "wikilink") and
[xf86-input-wacom](xf86-input-wacom "wikilink"). We also have a list of
[Supported Devices](/wiki/Supported_Devices "wikilink").

### So, what do I need?

On most desktop distributions, you need
[xf86-input-wacom](xf86-input-wacom "wikilink") as your X driver. Note
that distributions usually already ship a version of xf86-input-wacom in
their repositories. Kernel patches for Wacom devices, if they have been
submitted upstream, will also likely be available in your distribution's
kernel.

If your device is not supported by the current kernel and no Wacom
patches are pending, you may be lucky with the
[linuxwacom](linuxwacom "wikilink") kernel patches. Note that these
apply outside of your distribution and you will need to re-install each
time you update your distribution kernel.

User help
---------

Click **[here](/wiki/Category%3AHOWTO "wikilink")** for a list of **HOWTOs**.

Note that because [linuxwacom](linuxwacom "wikilink") is not necessarily
available in modern distributions anymore, **HOWTOs** and other
information that apply to the **[linuxwacom](linuxwacom "wikilink")**
tarballs are available separately
**[here](/wiki/Category%3ALinuxwacom "wikilink")**. If you run a modern
distribution, you will not need this.

The most commonly asked questions and the best answers available to date
are in the **[FAQ](/wiki/FAQ "wikilink")**. If you still have problem, please
check [Trackers](http://sourceforge.net/tracker/?group_id=69596) to see
if someone else has reported the issue or has a patch for the problem.

Mailing lists
-------------

IRC
---

The official linuxwacom [IRC](/wiki/IRC "wikilink") channel is
**\#linuxwacom** on **irc.freenode.net**. It is recommended if you ask
questions there that you also ask them on the mailing list. Please be
patient if you ask a question on IRC. People are not always watching the
channel.

Development
-----------

Please see the [DeveloperPages](/wiki/Category%3ADeveloperPages "wikilink")
for development-related information.

News
----

FAQ
---

Please see the **[FAQ](/wiki/FAQ "wikilink")** for more details.

Editing the wiki
----------------

If you want to edit this wiki, please create a user account and email
*peter.hutterer at who-t net* to be assigned editor rights. User rights
management is unfortunately manual. When editing, please follow the
[WikiEditingGuidelines](/wiki/WikiEditingGuidelines "wikilink").
