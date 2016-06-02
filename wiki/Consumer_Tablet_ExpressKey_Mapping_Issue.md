---
title: Consumer Tablet ExpressKey Mapping Issue
permalink: wiki/Consumer_Tablet_ExpressKey_Mapping_Issue/
layout: wiki
---

The GNOME desktop (and related offshoots like Unity, Cinnamon, etc.) has
a known compatibility issue with the buttons found on most "consumer"
Wacom devices that prevents them from being configured and used
properly. This issue has been pointed out on numerous occasions, but is
not likely to be resolved soon due to a lack of developer resources.
This page aims to document the issue and provide workarounds that may be
useful to affected users.

Affected Devices
----------------

This issue affects most "consumer" Wacom devices that were sold under
the "Bamboo" and (non-professional) "Intuos" brands. The following
devices, among others, may experience issues:

-   *CTH-690* Intuos Art medium
-   *CTL-690* Intuos Draw medium
-   *CTH-490* Intuos Art/Photo/Comic small
-   *CTL-490* Intuos Draw small
-   *CTL-680* Intuos Pen (M)
-   *CTL-480* Intuos Pen (S)
-   *CTH-680* Intuos Pen & Touch (M)
-   *CTH-480* Intuos Pen & Touch (S)
-   *CTL-671* One by Wacom
-   *CTH-670* Bamboo Create/Fun
-   *CTH-470* Bamboo Capture/Manga
-   *CTL-470* Bamboo Connect
-   *CTH-661SE* Bamboo Pen & Touch Special Edition (M)
-   *CTH-461SE* Bamboo Pen & Touch Special Edition (S)
-   *CTH-661(A)* Bamboo Fun/Comic Pen & Touch (M)
-   *CTH-461(A)* Bamboo Fun/Craft/Comic Pen & Touch (S)
-   *CTH-460(A)* Bamboo Pen & Touch
-   *CTH-661* Bamboo Fun/Comic Pen & Touch (M)
-   *CTH-461* Bamboo Fun/Craft/Comic Pen & Touch (S)
-   *CTH-460* Bamboo Pen & Touch
-   *CTT-460* Bamboo Touch
-   *CTE-650* Wacom Bamboo Fun
-   *CTE-450* Wacom Bamboo Fun
-   *CTE-640* Wacom Graphire4 6x8
-   *CTE-440* Wacom Graphire4 4x5

Issue Description
-----------------

Wacom has historically split their tablet lineup to better cater towards
two market segments: the professionals who demand performance and
flexibility, and consumers who place a premium on ease-of-use and price.
Because each segment is expected to deal with different use cases, Wacom
treats the pad buttons ("ExpressKeys") differently. Whereas the buttons
on a professional tablet are expected to be configured by a user to
improve their workflow, those on a consumer tablet are expected to
largely be used for navigational purposes. Following Wacom's example,
the open source community also treats the buttons differently; buttons
on the former are sequentially numbered (except for a "hole"
corresponding to the X11 scroll buttons 4-7), while those on the latter
use just the numbers of relevant navigation buttons. For example,
four-button consumer devices default to sending X11 buttons 1 (left
click), 3 (right click), 8 (navigate back), and 9 (navigate forward).

In order to allow buttons to perform actions that would not normally be
possible with the xf86-input-wacom driver, the GNOME desktop (and
related forks) has its `gnome-settings-daemon` process intercept any
button events sent by the tablet. The received button number is used as
an index into a list of actions which tells GNOME what it should do in
response. This works well for professional devices where the buttons are
sequential, but breaks for consumer devices. Consider the case of a
four-button consumer device: the first button sends X11 button 1 which
is correctly interpreted by GNOME, the second button sends X11 button 3
which is treated like the third button, the third button sends X11
button 8 which is treated like the fourth button (remember the "hole"
mentioned above), and the fourth button sends X11 button 9 which is
ignored since there is no fifth button for it to act like.

This behavior was noticed sometime around 2012, but GNOME was of the
opinion that it was not their problem to resolve. They have no way of
knowing what X11 buttons will be sent by our driver for any given
button, so the best they could do is guess based on information
available through libinput and other sources. Changing our driver to use
sequential numbering for both consumer and professional tablets would
fix the issue, but at a cost of the default behavior in other desktop
environments being broken. Other solutions have been proposed (e.g.
modifying libinput's tablet "Button" property to reflect the X11 button
numbers instead of physical button numbers) but none have gained
traction as an appropriate solution that is agreeable to both linuxwacom
and GNOME developers.

### Workarounds

#### wacom-gnome-compat.sh

The
[`wacom-gnome-compat.sh`](https://gist.github.com/jigpu/fde784840ba5731726e7382952b0d5ed)
script was created as a workaround that allows you to configure the X
driver so that your tablet sends the X11 buttons expected by GNOME. The
script asks you to press each button on your tablet in turn and then 'q'
to quit. Once done, it provides both a list of xsetwacom commands that
can be used to temporarily change the button assignments, as well as an
[xorg.conf.d snippet](/wiki/Xorg.conf.d "wikilink") that is a more permanant
and hassle-free change.

#### xsetwacom

Another option is to not use the GNOME Control Center at all and instead
use [xsetwacom](xsetwacom "wikilink") to configure the tablet buttons to
act as keyboard keys. See [Tablet
Configuration](/wiki/Tablet_Configuration "wikilink") for instructions on how
to use xsetwacom. The primary downsides to using xsetwacom are that you
can't use it to send mouse buttons to applications (since GNOME will
intercept them [unless
disabled](/wiki/Tablet_Configuration#gnome-settings-daemon "wikilink")), and
that the commands must be re-run any time the X server is restarted or
the tablet reconnected (so you may want to use a startup script like the
one discussed in [Tablet
Configuration](/wiki/Tablet_Configuration#Sample_Runtime_Script "wikilink")).

For example, you could do something like:

`xsetwacom set "Wacom Bamboo Pad pad" Button 1 "key esc"`  
`xsetwacom set "Wacom Bamboo Pad pad" Button 3 "key F11"`  
`xsetwacom set "Wacom Bamboo Pad pad" Button 8 "key ctrl alt t"`  
`xsetwacom set "Wacom Bamboo Pad pad" Button 9 "key p"`  

after running **xsetwacom --list** to get the name of the device in the
first set of quotes.

Discussions
-----------

Relevant fix discussions:

-   <https://sourceforge.net/p/linuxwacom/mailman/message/32096190/>
-   <https://sourceforge.net/p/linuxwacom/bugs/244/>
-   <https://sourceforge.net/p/linuxwacom/bugs/266/>
-   <https://sourceforge.net/p/linuxwacom/bugs/266/>
-   <https://sourceforge.net/p/linuxwacom/bugs/268/>
-   <https://sourceforge.net/p/linuxwacom/bugs/291/>

Relevant Developer Discussions:

-   <https://www.mail-archive.com/linuxwacom-devel%40lists.sourceforge.net/msg04896.html>
-   <https://www.mail-archive.com/linuxwacom-devel%40lists.sourceforge.net/msg04918.html>
-   <https://www.mail-archive.com/linuxwacom-devel%40lists.sourceforge.net/msg05980.html>
-   <http://sourceforge.net/p/linuxwacom/mailman/message/32091384/>

Other Discussions:

-   <http://who-t.blogspot.com/2016/04/libinput-and-graphics-tablet-pad-support.html>
