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
**The Linux Wacom Project** manages the drivers, libraries, and
documentation for configuring and running Wacom tablets under the Linux
operating system.

|                                                                     |                       |                                                            |                          |                                         |                                   |
|---------------------------------------------------------------------|-----------------------|------------------------------------------------------------|--------------------------|-----------------------------------------|-----------------------------------|
| \[\[<File:Document-save.png>                                        | link=Downloads\]\]    | \[\[<File:Help-browser.png>                                | link=:Category:HOWTO\]\] | \[\[<File:Applications-development.png> | link=:Category:DeveloperPages\]\] |
| \[\[Downloads                                                       | Get the Drivers\]\]   | \[\[Category%3AHOWTO                                       | HOWTOs\]\]               | \[\[Category%3ADeveloperPages           | Developer Pages\]\]               |
| [Legacy Drivers](http://sourceforge.net/projects/linuxwacom/files/) | [FAQ](/wiki/FAQ "wikilink") | [Trackers](http://sourceforge.net/tracker/?group_id=69596) |                          |                                         |                                   |
|                                                                     | \[\[:Mailing lists    | Mailing List\]\]                                           |                          |                                         |                                   |
|                                                                     | [IRC](/wiki/IRC "wikilink") |                                                            |                          |                                         |                                   |

For your tablet to work, two pieces are required. Both of these pieces
are available through this project:

-   **Kernel Driver:** The kernel driver converts the hardware-specific
    protocol (serial or USB) into standard kernel input events. These
    events can then easily be read by user-space applications without
    requiring (too much) hardware-specific knowledge. We try to upstream
    our kernel drivers as quickly as possible and hence these drivers
    are usually included in your distribution kernel. We have
    alternative kernel patches available for those that are bound to a
    specific kernel version (see [Downloads](/wiki/Downloads "wikilink")).

<!-- -->

-   **[X.Org X11](http://www.x.org) Driver:** The X driver splits the
    device into multiple subdevices, usually one for the stylus, one for
    the eraser, one for touch, etc. These devices then allow
    applications like the [GIMP](http://www.gimp.org) to access extended
    functionality like pressure and high-resolution coordinates. The [ X
    drivers](xf86-input-wacom "wikilink") are released often and usually
    packaged by distributions. If you require a newer version (or you
    are stuck with an old X server), please see our
    [Downloads](/wiki/Downloads "wikilink") page for other options.

Legacy [linuxwacom](linuxwacom "wikilink") drivers are still maintained,
but have been phased out in preference of the new
[xf86-input-wacom](xf86-input-wacom "wikilink") and upstream kernel
drivers. Help for legacy drivers may be found
[here](/wiki/Category%3ALinuxwacom "wikilink"). If you run a modern
distribution, you will not need this.

For more information about the bits and pieces provided, read the
[History of the Linux Wacom Project](/wiki/History "wikilink").

News
----
