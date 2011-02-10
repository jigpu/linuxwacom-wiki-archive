---
title: External applications
permalink: wiki/External_applications/
layout: wiki
tags:
 - HOWTO
---

This page is a list of external applications that interface with the
wacom driver in one way or another.

Utilities
=========

CellWriter
----------

CellWriter is a grid-entry natural handwriting input panel. As you write
characters into the cells, your writing is instantly recognized at the
character level. When you press Enter on the panel, the input you
entered is sent to the currently focused application as if typed on the
keyboard. It also toggles to an on-screen keyboard. You distribution
should offer it in its package manager. Located at:
<http://risujin.org/cellwriter/> and at:
<http://code.google.com/p/cellwriter/>

Magick Rotation
---------------

Magick Rotation is a Python GTK app. that supports automatic rotation
for Dell, HP, and Lenovo tablet PCs. It's located on Launchpad at:
<https://launchpad.net/magick-rotation>

xinput\_calibrator
------------------

The xinput\_calibrator is a generic touchscreen calibration program for
X.Org. It's located at freedesktop.org:
<http://www.freedesktop.org/wiki/Software/xinput_calibrator>

Graphical Configuration Tools
=============================

GNOME
-----

-   [gnome-settings-daemon](http://git.gnome.org/browse/gnome-settings-daemon/commit/?id=9863ccf9d99fdd712778b31197365723b9caa0behttp://git.gnome.org/browse/gnome-settings-daemon/commit/?id=9863ccf9d99fdd712778b31197365723b9caa0be)
    already supports several wacom-related
    [gsettings](http://library.gnome.org/devel/gio/2.25/GSettings.html)
    keys for configuration. This includes hotplugging support so that
    the configuration options are applied both after login and whenever
    a tablet is plugged in.

<!-- -->

-   A [GNOME
    control-center](http://git.gnome.org/browse/gnome-control-center)
    applet is currently under development. See [GNOME Bug
    640981](https://bugzilla.gnome.org/show_bug.cgi?id=640981).

wacomcpl
--------

wacomcpl was a Tcl/Tk-based GUI tool shipped as part of the
[linuxwacom](linuxwacom "wikilink") tarball. It is now unsupported and
**does not work** with [xf86-input-wacom](xf86-input-wacom "wikilink").
The design of wacomcpl is not suited to hotplugging and it will not be
updated. Since wacomcpl does not integrate with any desktop environment,
any work on wacomcpl is a dead end. **Do not work on wacomcpl, invest
your efforts on integrating wacom support in your desktop environment
instead.**
