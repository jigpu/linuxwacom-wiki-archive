---
title: Coding Style
permalink: wiki/Coding_Style/
layout: wiki
tags:
 - DeveloperPages
---

This page describes the coding style used in
[xf86-input-wacom](xf86-input-wacom "wikilink"). If you submit a patch
please make sure the style adheres to this guideline or the patch may be
rejected.

C coding style
==============

-   Tab is set to 8 characters
-   Default indentation is one tab
-   Indentation with tabs and remaining spaces to lign up code where
    necessary. Example below shows tabs with "*&gt;----*" and spaces
    with "*.*".

<!-- -->

    >-------if.(InitValuatorClassDeviceStruct(pInfo->dev, nbaxes,
    #if.GET_ABI_MAJOR(ABI_XINPUT_VERSION) >= 7
    >------->------->------->------->-------..axis_labels,
    #endif
    >-------                ..GetMotionHistorySize(),
    >-------                ..(is_absolute(pInfo) ?  Absolute : Relative) | OutOfProximity) == FALSE)

-   Opening curly braces { go on a new line. Closing curly braces } go
    on the same line as else. e.g.

<!-- -->

    if (condition)
    {
            do_something();
    } else {
            do_something_else();
    }

-   Declarations *should* be aligned. e.g.

<!-- -->

    int a   = 0;
    int foo = 10;
    int bar = 11;

-   No [hungarian
    notation](http://en.wikipedia.org/wiki/Hungarian_notation). There
    are a few exceptions for this (*pInfo*, *pDev* and some remainders
    of old code).
-   Use const for pointers where appropriate.

Each new file requires the following line at the bottom of the file:

` /* vim: set noexpandtab tabstop=8 shiftwidth=8: */`
