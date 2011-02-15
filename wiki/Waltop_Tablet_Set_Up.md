---
title: Waltop Tablet Set Up
permalink: wiki/Waltop_Tablet_Set_Up/
layout: wiki
tags:
 - HOWTO
---

This page is for general tablet issues & tips as well as model specific
issues & tips.

The linuxwacom project welcomes additions to its mediawiki so please
join in if you have contributions.

Be aware the Wacom drivers are rapidly evolving and some of this
information may become dated. If so feel free to update and correct the
mediawiki.

Set Up
------

If you have at least the 2.6.35 kernel, particularly the more recent
versions, you should be able to get your tablet working on the Wacom
driver. Patches have been submitted by Nikolai Kondrashov to the kernel
that should improve Waltop tablet compatibility with the Wacom driver
starting in the 2.6.36 or 2.6.37 kernels. Waltop tablets are often
re-branded and will carry another OEM's name. To verify that your
tablet/stylus is indeed identified as a WALTOP (and to determine the
identifier for the match line) enter in a terminal the following.

    xinput list

You should see WALTOP (capitalized) in the tablet/stylus line. If you
see another tablet name in the line you do not have a Waltop. Use the
version of Waltop you see in the match line for the following usb
snippet in 50-wacom.conf. The usb snippet is easy to identify because it
has a couple of comment lines discussing Waltops. Just change the
snippet to look like the following.

    Section "InputClass"
        Identifier "Wacom class"
        MatchProduct "Wacom|WACOM|Hanwang|WALTOP"
        MatchDevicePath "/dev/input/event*"
        Driver "wacom"
    EndSection

And that should be it. After a reboot your Waltop should now be using
the Wacom X driver [xf86-input-wacom](xf86-input-wacom "wikilink").
Since it is a system file you need root privileges. If your system has
sudo installed you can use

    gksudo gedit /usr/share/X11/xorg.conf.d/50-wacom.conf

It's possible additional changes to support your Waltop may have been
made since your distribution's default version of
[xf86-input-wacom](xf86-input-wacom "wikilink"). If your distribution
hasn't been updating it you may want to consider cloning it from the git
repository.

**Tip** If you have problems with both stylus buttons giving the same
response despite being set to different values please report your issue
to
[linuxwacom-discuss](https://lists.sourceforge.net/lists/listinfo/linuxwacom-discuss).

Calibration of Your Stylus
--------------------------

See the [Calibration HOWTO](/wiki/Calibration "wikilink").

Dual and Multi-Monitor Set Up
-----------------------------

See [Dual and Multi-Monitor Set
Up](/wiki/Dual_and_Multi-Monitor_Set_Up "wikilink").

Graphical Configuration GUI
---------------------------

Work is proceeding on a replacement for wacomcpl. See [Graphical
Configuration
Tools](/wiki/External_applications#Graphical_Configuration_Tools "wikilink").

Tablet Configuration
--------------------

Currently [xf86-input-wacom](xf86-input-wacom "wikilink") does not have
a configuration gui like linuxwacom's Wacom Control Panel (wacomcpl).
However since **wacomcpl** stored its settings as a
[xsetwacom](xsetwacom "wikilink") script in **.xinitrc** we can at least
duplicate the .xinitrc, calling it say **.xsetwacom.sh**. Below is a
sample script for your stylus. **This is a temporary hack** while we
wait on a replacement for wacomcpl.

While doing things this way may seem a little strange at first you'll
find yourself quickly adapting. And this allows you some of the finer
control over your stylus features that wacomcpl gave you.

**Note** While these xsetwacom commands were working as of 2-1-11 be
aware that the xsetwacom interface may change without notice. If this
happens feel free to update and correct this linxwacom mediawiki page.

If a xsetwacom command stops working your friend is the get command.

### Installing the Script

While you can change your settings on the fly using xsetwacom commands
in a terminal they only apply during the current session. So you need to
set it up to auto-start. Create a file and copy & paste the contents of
the script you want into it. Rename it .xsetwacom.sh (or whatever you
want) and place it in your home directory. Remember it will be a hidden
file because of the . in front. To enable the xsetwacom commands in the
.xsetwacom.sh file to apply to Xserver through a reboot you enter in a
terminal.

    chmod +x ~/.xsetwacom.sh

Or you could right click on the file and in Properties, in the
Permission tab, check Execute as program. Then go to
System-&gt;Preferences-&gt;Startup Applications (or your distributions
equivalent) and click on Add and for the command write "sh
/home/yourusername/.xsetwacom.sh" (without the quotes).

Once the script is executable you can double click on it to apply it's
settings or reboot to check the auto-start set up.

### The Sample Waltop Xsetwacom Script

The following script was authored by Favux and developed with
stefan-koch as cited in the script. All of the values are the defaults
with the default stated and the appropriate range given. The script is
intended to provide you with a framework to configure your settings in a
text file rather than at at the command line, with similar results to
what the wacomcpl gui would have produced.

In the example script below both "device name" and ID \# are used. Be
sure to check for yours using 'xinput list' (without the quotes) in a
terminal and use them. If you are hot plugging your tablet or other
devices be sure to use "device name" as the ID \# can change.

    ## Device names and ID numbers from 'xinput list' entered in a terminal.
    #
    ## In the example both "Device name" and ID # are used.
    #
    ## If you are hot plugging use "Device name" as ID # can change.
    #
    ## ClickForce changes name to Threshold with xf86-input-wacom 0.10.9 (11-19-10)
    #
    ## Warning:  Changing Mode to either Absolute or Relative in the script stops the
    ## mouse from being able to pull guidelines out of the ruler in Gimp.

    ## stylus = ID 12 = "WALTOP International Corp. Slim Tablet stylus"
    xsetwacom set "WALTOP International Corp. Slim Tablet stylus" Suppress "4"  # data pt.s trimmed, default is 4, 0-20
    xsetwacom set "WALTOP International Corp. Slim Tablet stylus" RawSample "2"  # data pt.s filtered, default is 2, 0-100 
    xsetwacom set "WALTOP International Corp. Slim Tablet stylus" ClickForce "27"  # pressure, default is 27, range is 0-2047
    #xsetwacom set "WALTOP International Corp. Slim Tablet stylus" Threshold "27"  # pressure, default is 27, range is 0-2047
    xsetwacom set "WALTOP International Corp. Slim Tablet stylus" PressCurve "5 10 90 95" # Bezier curve, default is 0,0,100,100
    xsetwacom set "WALTOP International Corp. Slim Tablet stylus" TPCButton "on"  # stylus tip + button, or "off" for hover mode
    #xsetwacom set 12 Mode "Absolute"  # or Relative cursor movement
    xsetwacom set 12 Button1 "1"  # left mouse click
    xsetwacom set 12 Button2 "3"  # right mouse click
    xsetwacom set 12 Button3 "2"  # middle mouse click
    ## if needed, add your tablet's coordinates
    #xsetwacom set "WALTOP International Corp. Slim Tablet stylus" topy "0"
    #xsetwacom set "WALTOP International Corp. Slim Tablet stylus" topx "0"
    #xsetwacom set "WALTOP International Corp. Slim Tablet stylus" bottomy "11250"
    #xsetwacom set "WALTOP International Corp. Slim Tablet stylus" bottomx "20000"

    ## Developed with stefan-koch @ Ubuntu forums
