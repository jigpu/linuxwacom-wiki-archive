---
title: Fixed device files with udev
permalink: wiki/Fixed_device_files_with_udev/
layout: wiki
tags:
 - HOWTO
---

<noinclude> The USB Wacom tablet may be assigned to a different
*/dev/input/event\#* after each reboot. To resolve this issue, you can
link Wacom USB tablet to */dev/input/wacom*. It can be done by adding
the following rules in */etc/udev/rules.d/60-wacom.rules*. Some
distributions use a different number for the file name. Please look for
proper one with wacom under */etc/udev/rules.d*.

Below are examples from Debian GNU/Linux distribution: </noinclude>
Systems with only one Wacom device:

        # udev rules for wacom tablets.

        KERNEL!="event[0-9]*", GOTO="wacom_end"

        # Multiple interface support for stylus and touch devices.
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        # Convenience links for the common case of a single tablet.  We could do just this:
        #ATTRS{idVendor}=="056a", SYMLINK+="input/wacom-$env{WACOM_TYPE}"
        # but for legacy reasons, we keep the input/wacom link as the generic stylus device.
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

        # Check and repossess the device if a module other than the wacom one
        # is already bound to it.
        ATTRS{idVendor}=="056a", ACTION=="add", RUN+="check_driver wacom $devpath $env{ID_BUS}"

        LABEL="wacom_end"

Systems with more than one Wacom devices:

        # udev rules for wacom tablets.
        # These rules were compiled for the Debian GNU/Linux distribution,
        # but others may, and indeed are encouraged to, use them also.
        #
        # Should you do so, PLEASE CO-ORDINATE ANY CHANGES OR ADDITIONS
        # of new devices with Ron  so that we can try
        # to present users with a standard set of device nodes
        # which they can rely on across the board.

        KERNEL!="event[0-9]*", GOTO="wacom_end"

        # Port specific link for users of multiple tablets of the same type.
        # The ID_PATH variable is set by the "path_id" script in an earlier rule file.
        ATTRS{idVendor}=="056a", ENV{ID_PATH}=="?*", SYMLINK="input/by-path/$env{ID_PATH}-wacom"

        # Multiple interface support for stylus and touch devices.
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="00", ENV{WACOM_TYPE}="stylus"
        DRIVERS=="wacom", ATTRS{bInterfaceNumber}=="01", ENV{WACOM_TYPE}="touch"

        # Type-named links for multiple tablets.  If you want to use multiple
        # tablets of the _same_ type, you will probably need to use the links
        # from /dev/input/by-path to identify which is plugged into what usb
        # port.  For different tablet types though, just pick your links from
        # the list below.
        #
        # We override SYMLINK for tabletpc devices because the by-path link
        # is not required with such devices, there will only ever be one.
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0000", SYMLINK+="input/tablet-penpartner"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0003", SYMLINK+="input/tablet-cintiq_partner"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0010", SYMLINK+="input/tablet-graphire"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0011", SYMLINK+="input/tablet-graphire2-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0012", SYMLINK+="input/tablet-graphire2-5x7"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0013", SYMLINK+="input/tablet-graphire3"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0014", SYMLINK+="input/tablet-graphire3-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0015", SYMLINK+="input/tablet-graphire4-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0016", SYMLINK+="input/tablet-graphire4-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0017", SYMLINK+="input/tablet-bamboofun-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0018", SYMLINK+="input/tablet-bamboofun-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0019", SYMLINK+="input/tablet-bamboo1-medium"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0020", SYMLINK+="input/tablet-intuos-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0021", SYMLINK+="input/tablet-intuos-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0022", SYMLINK+="input/tablet-intuos-9x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0023", SYMLINK+="input/tablet-intuos-12x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0024", SYMLINK+="input/tablet-intuos-12x18"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0030", SYMLINK+="input/tablet-pl400"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0031", SYMLINK+="input/tablet-pl500"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0032", SYMLINK+="input/tablet-pl600"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0033", SYMLINK+="input/tablet-pl600sx"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0034", SYMLINK+="input/tablet-pl550"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0035", SYMLINK+="input/tablet-pl800"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0037", SYMLINK+="input/tablet-pl700"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0038", SYMLINK+="input/tablet-pl510"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0039", SYMLINK+="input/tablet-dtu710"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="003f", SYMLINK+="input/tablet-cintiq21ux"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0041", SYMLINK+="input/tablet-intuos2-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0042", SYMLINK+="input/tablet-intuos2-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0043", SYMLINK+="input/tablet-intuos2-9x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0044", SYMLINK+="input/tablet-intuos2-12x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0045", SYMLINK+="input/tablet-intuos2-12x18"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0047", SYMLINK+="input/tablet-intuos2-6x8a"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0060", SYMLINK+="input/tablet-volito"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0061", SYMLINK+="input/tablet-penstation2"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0062", SYMLINK+="input/tablet-volito2-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0063", SYMLINK+="input/tablet-volito2-2x3"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0064", SYMLINK+="input/tablet-penpartner2"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0065", SYMLINK+="input/tablet-bamboo"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0069", SYMLINK+="input/tablet-bamboo1"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0081", SYMLINK+="input/tablet-graphire_bt-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0090", SYMLINK="input/tablet-tpc90"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="0093", SYMLINK="input/tablet-tpc93-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="009a", SYMLINK="input/tablet-tpc9a-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b0", SYMLINK+="input/tablet-intuos3-4x5"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b1", SYMLINK+="input/tablet-intuos3-6x8"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b2", SYMLINK+="input/tablet-intuos3-9x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b3", SYMLINK+="input/tablet-intuos3-12x12"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b4", SYMLINK+="input/tablet-intuos3-12x19"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b5", SYMLINK+="input/tablet-intuos3-6x11"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b7", SYMLINK+="input/tablet-intuos3-4x6"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b8", SYMLINK+="input/tablet-intuos4-4x6"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00b9", SYMLINK+="input/tablet-intuos4-6x9"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00ba", SYMLINK+="input/tablet-intuos4-8x13"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00bb", SYMLINK+="input/tablet-intuos4-12x19"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c0", SYMLINK+="input/tablet-dtf521"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c4", SYMLINK+="input/tablet-dtf720"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c5", SYMLINK+="input/tablet-cintiq20wsx"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c6", SYMLINK+="input/tablet-cintiq12wx"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00c7", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/tablet-dtu1931"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d0", SYMLINK="input/tablet-bamboo2fg-touch-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d1", SYMLINK="input/tablet-bamboo2fg-pt-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d2", SYMLINK="input/tablet-bamboo2fg-craft-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d3", SYMLINK="input/tablet-bamboo2fg-fun-6x8-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d4", SYMLINK+="input/tablet-bamboo-p-4x5"    
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d6", SYMLINK="input/tablet-bamboo2fg-newpt-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d7", SYMLINK="input/tablet-bamboo2fg-fun-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00d8", SYMLINK="input/tablet-bamboo2fg-comic-6x8-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00dA", SYMLINK="input/tablet-bamboo4fg-se-4x5-$env{WACOM_TYPE}"
        ATTRS{idVendor}=="056a", ATTRS{idProduct}=="00dB", SYMLINK="input/tablet-bamboo4fg-se-6x8-$env{WACOM_TYPE}"

        # Convenience links for the common case of a single tablet.  We could do just this:
        #ATTRS{idVendor}=="056a", SYMLINK+="input/wacom-$env{WACOM_TYPE}"
        # but for legacy reasons, we keep the input/wacom link as the generic stylus device.
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}!="touch", SYMLINK+="input/wacom"
        ATTRS{idVendor}=="056a", ENV{WACOM_TYPE}=="touch", SYMLINK+="input/wacom-touch"

        # Check and repossess the device if a module other than the wacom one
        # is already bound to it.
        ATTRS{idVendor}=="056a", ACTION=="add", RUN+="check_driver wacom $devpath $env{ID_BUS}"

        LABEL="wacom_end"

<noinclude>

Newer Mandriva Linux (Mandriva 2007 Spring and later) has an application
called mousedrake which takes care of the setup and configuration of
linuxwacom driver. If you use Mandriva Linux and you see InputDevice
sections for Wacom device in your Xorg.conf, chances are your Wacom
tablet is ready for you to draw.

</noinclude>
