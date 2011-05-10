---
title: Main Page
permalink: wiki/Main_Page/
layout: wiki
---

<div style="padding:5px; border:1px solid #ffcc00; text-align:center; background:#FFFDDF; width:100%; margin-bottom:.2em;">
We switched from the old website to a
[MediaWiki](http://www.mediawiki.org). During the transition period, the
old website is still available
[here](http://linuxwacom.sourceforge.net/index_old.php).

</div>
Our sourceforge project page can be found at
<https://sourceforge.net/projects/linuxwacom>.

The Linux Wacom Project
=======================

The Linux Wacom Project manages the drivers, libraries, and
documentation for configuring and running Wacom tablets under the Linux
operating system. For your tablet to work, you need two pieces:

-   **kernel drivers:** The kernel driver converts the hardware-specific
    protocol (serial or USB) into the standard kernel events. These
    events can then easily be read by user-space applications without
    requiring (too much) hardware-specific knowledge. We try to upstream
    our kernel drivers as quickly as possible and hence these drivers
    are usually included in your distribution kernel. We have
    alternative kernel patches available for those that are bound to a
    specific kernel version (see [Downloads](/wiki/Downloads "wikilink")).

<!-- -->

-   **[X.Org X11](http://www.x.org) driver:** The X driver splits the
    device into multiple subdevices, usually one for the stylus, one for
    the eraser, one for touch, etc. These devices then allow
    applications like the [GIMP](http://www.gimp.org) to access extended
    functionality like pressure and high-resolution coordinates. The [ X
    drivers](xf86-input-wacom "wikilink") are released often and usually
    packaged by distributions. If you require a newer version (or you
    are stuck with an old X server), please see our
    [Downloads](/wiki/Downloads "wikilink") page for other options.

Downloads
---------

For a list of files available, please visit the
[Downloads](/wiki/Downloads "wikilink") page.

User help
---------

Click **[here](/wiki/Category%3AHOWTO "wikilink")** for a list of **HOWTOs**.

Note that because [linuxwacom](linuxwacom "wikilink") is not necessarily
available in modern distributions anymore, **HOWTOs** and other
information that apply to the [linuxwacom
tarballs](http://sourceforge.net/projects/linuxwacom/files/) are
available separately **[here](/wiki/Category%3ALinuxwacom "wikilink")**. If
you run a modern distribution, you will not need this.

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
