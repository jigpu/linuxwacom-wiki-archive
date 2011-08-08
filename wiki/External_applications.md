---
title: External applications
permalink: wiki/External_applications/
layout: wiki
tags:
 - HOWTO
---

This page is a list of external applications that can make use of Wacom
tablets or TabletPCs under Linux. Please keep this list short and
representitive of the best programs in their respective fields.

Graphics
--------

-   **[GIMP](http://www.gimp.org/)**: A freely distributed program for
    such tasks as photo retouching, image composition and image
    authoring. Brushes support pressure, tilt, and rotation.

<!-- -->

-   **[Inkscape](http://inkscape.org/)**: A vector graphics editor with
    capabilities similar to Illustrator, CorelDraw, or Xara X.
    Calligraphy tool supports pressure and tilt.

Handwriting
-----------

-   **[CellWriter](http://code.google.com/p/cellwriter/)**: A grid-entry
    natural handwriting input panel. As you write characters into the
    cells, your writing is instantly recognized at the character level.
    When you press Enter on the panel, the input you entered is sent to
    the currently focused application as if typed on the keyboard. It
    also toggles to an on-screen keyboard. Also available at
    [1](http://risujin.org/cellwriter/)

<!-- -->

-   **[Xournal](http://xournal.sourceforge.net/)**: An application for
    notetaking, sketching, or keeping a journal using a stylus.

<!-- -->

-   **[jarnal](http://jarnal.wikispaces.com/)**: An application for
    individual or collaborative notetaking, sketching, etc.

Utilities
---------

### Graphical Configuration Tools

-   **[Wacom Control
    Panel](http://gtk-apps.org/content/show.php/Wacom+Control+Panel?content=104309)**:
    This utility allows you to automatically detect and configure a wide
    range of wacom products. Automatically remembers and reapplies
    settings on startup.

<!-- -->

-   **[gnome-control-center](http://git.gnome.org/browse/gnome-control-center)**:
    An applet to support tablet configuration from the GNOME control
    center is under development. See [GNOME Bug
    640981](https://bugzilla.gnome.org/show_bug.cgi?id=640981).

<!-- -->

-   **[Wacom
    Tablet](http://kde-apps.org/content/show.php/wacom+tablet?content=114856)**:
    A KDE 4 KCModule. This KDE system tool implements a GUI for the
    Wacom Linux Drivers and extends it with profile support to handle
    different button/pen layouts per profile.

<!-- -->

-   **wacomcpl**: A Tcl/Tk-based GUI tool shipped as part of the
    [linuxwacom](linuxwacom "wikilink") tarball. It is now unsupported
    and **does not work** with
    [xf86-input-wacom](xf86-input-wacom "wikilink"). The design of
    wacomcpl is not suited to hotplugging and it will not be updated.
    Since wacomcpl does not integrate with any desktop environment, any
    work on wacomcpl is a dead end. **Do not work on wacomcpl, invest
    your efforts on integrating wacom support in your desktop
    environment instead.**

### Other Utilities

-   **[wdaemon](wdaemon "wikilink")**: A hotplugging daemon for wacom
    devices.

<!-- -->

-   **[Magick Rotation](https://launchpad.net/magick-rotation)**: An
    application that will help Dell, HP, and Lenovo tablet pc's
    automatically rotate screen orientation and devices that use the
    Wacom or evdev drivers.

<!-- -->

-   **[xinput\_calibrator](http://www.freedesktop.org/wiki/Software/xinput_calibrator)**:
    A generic touchscreen calibration program for X.Org.

<!-- -->

-   **[easystroke](https://sourceforge.net/apps/trac/easystroke/)**: A
    gesture-recognition application for X11. Gestures can be made to
    emulate key presses, execute shell commands, hold down modifiers and
    emulate a scroll wheel.

Libraries / Toolkits / Frameworks
---------------------------------

-   **[gnome-settings-daemon](http://git.gnome.org/browse/gnome-settings-daemon/commit/?id=9863ccf9d99fdd712778b31197365723b9caa0behttp://git.gnome.org/browse/gnome-settings-daemon/commit/?id=9863ccf9d99fdd712778b31197365723b9caa0be)**
    already supports several wacom-related
    [gsettings](http://library.gnome.org/devel/gio/2.28/GSettings.html)
    keys for configuration. This includes hotplugging support so that
    the configuration options are applied both after login and whenever
    a tablet is plugged in.
