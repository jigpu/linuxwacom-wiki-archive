---
title: Wacom-kernel Repository
permalink: wiki/Wacom-kernel_Repository/
layout: wiki
tags:
 - DeveloperPages
---

The wacom-kernel git repository is a recent reflection of the current
[linux kernel](http://kernel.org/), and is intended to be used to assist
Wacom kernel module development. To check how closely synchronized
wacom-kernel is with the **mainline** kernel select *Gitweb* to browse
the kernel's git repository.

wacom-kernel
------------

The git repository is available to [browse
here](http://sourceforge.net/p/linuxwacom/wacom-kernel/ref/master~/). It
consists of 3 branches: devel, master, and tested.

To clone the wacom-kernel git repository use:

    git clone git://git.code.sf.net/p/linuxwacom/wacom-kernel

Working with a Wacom Module
---------------------------

The devel branch is intended for working with the modules. Use *git
checkout* to select it:

    cd wacom-kernel
    git checkout devel

Depending on the module you wish to work with change to the appropriate
directory.

-   wacom.ko files: wacom.h, wacom\_sys.c, wacom\_wac.c, wacom\_wac.h
    -   in drivers/input/tablet
-   hid-wacom.ko file: hid-wacom.c
    -   in drivers/hid
-   wacom\_w8001.ko file: wacom\_w8001.c
    -   in drivers/input/touchscreen

Make your changes. There is a Makefile in the wacom-kernel/wacom-kernel
directory that makes it a bit easier to compile & install the wacom
modules. See Makefile Options below.

Makefile Options
----------------

Selected Wacom modules can be built and installed with the different
wacom-kernel/wacom-kernel/Makefile options.

### Current Module Build and Install Options

1.  **"make"**:
    -   builds all modules
2.  **modules**
    1.  **"make modules"** - builds:
        -   drivers/input/tablet/wacom.ko
    2.  **"make install"** - installs:
        -   drivers/input/tablet/wacom.ko
3.  **btmodules**
    1.  **"make btmodules"** - builds:
        -   drivers/hid/hid-wacom.ko
        -   drivers/hid/hid.ko
        -   drivers/hid/usbhid/usbhid.ko
    2.  **"make btinstall"** - installs:
        -   drivers/hid/hid-wacom.ko
        -   drivers/hid/hid.ko
        -   drivers/hid/usbhid/usbhid.ko
4.  **tsmodules**
    1.  **"make tsmodules"** - builds:
        -   drivers/input/touchscreen/wacom\_w8001.ko
    2.  **"make tsinstall"** - installs:
        -   drivers/input/touchscreen/wacom\_w8001.ko
5.  **"make clean"**:
    -   deletes all wacom related modules and build files

Sample Workflow
---------------

A possible workflow could consist of the following:

1.  Make changes to the module code.
2.  Add the changed file with *git add* and sometimes do a *git commit*.
3.  Then cd to wacom-kernel (use separate terminal for that) and *make
    modules* (might use the alternative *make btmodules* if working on
    hid-wacom).
4.  If the compilation goes OK do a *make install* from a separate
    terminal with root account. (could use *make btinstall* if you used
    *make btmodules* in 3).
5.  Now *rmmod* the new module and after restarting the tablet the new
    module is loaded.
6.  Test if the changes worked.

A more concrete example is Przemo's typical workflow for the Intous4 W
(he does all work from the main kernel directory).

1.  Make changes to drivers/hid/hid-wacom.c:
    -   vim drivers/hid/hid-wacom.c
2.  Commit the changes:
    -   git commit -a -s -m "HID: wacom: My patch"

      
    -a all files,

    -s my signature,

    -m name of the patch

3.  Switch off the tablet.
4.  Run "make btmodules && sudo rmmod hid-wacom && sudo btinstall".
5.  Turn on the tablet - setting up bluetooth connection triggers
    loading of the hid-wacom.ko module.
6.  Test changes.
7.  If the changes aren't good go back to 1.
8.  If the changes are good enough to be submitted generate a patch:
    -   git format-patch HEAD~1
9.  Check if the patch is OK:
    -   scripts/checkpatch.pl 0001-HID-wacom-My-patch.patch
10. Submit patch to linuxwacom-devel list:
    -   git send-email --to linuxwacom-devel@lists.sourceforge.net
        0001-HID-wacom-My-patch.patch

Maintainer
----------

The repository is maintained by Przemo Firszt.

Please do not send email regarding bugs or feature requests to the
maintainer. Use the [mailing lists](mailing_lists "wikilink") instead.
