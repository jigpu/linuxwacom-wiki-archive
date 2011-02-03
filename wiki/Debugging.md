---
title: Debugging
permalink: wiki/Debugging/
layout: wiki
tags:
 - DeveloperPages
---

This page provides an overview on how to debug issues with the driver.

Enabling in-driver debugging statements
=======================================

The simplest way to debug the driver is to enable the debug statements
in the driver itself. There are two types of debug statements that can
be enabled separately with [xsetwacom](xsetwacom "wikilink"):

-   **DebugLevel** enables debug statements for code paths specific to a
    tool.
-   **CommonDBG** enables debug statements for code paths generic for
    all tools on a physical tablet.

Each type takes a debug levels between 0 (debugging off) and 12. The
higher the level, the more debug messages you'll see. All debug messages
are printed to the Xorg.log file.

Example debug output:

    [241820.117] (II) Wacom Intuos4 6x9 pad (7:wcmSendEvents): [PAD] o_prox=false x=0 y=0 z=0 b=false b=0 tx=0 ty=0 wl=53 rot=-875 th=0
    [241820.117] (II) Wacom Intuos4 6x9 pad (10:wcmRotateAndScaleCoordinates): rotate/scaled to 0/0
    [241820.117] (II) Wacom Intuos4 6x9 pad (6:wcmSendEvents): abs prox=1   x=0 y=0 z=0 v3=0    v4=0    v5=53   id=15   serial=4294967295   button=false    buttons=0
    [241820.117] (II) Wacom Intuos4 6x9 pad (6:wcmSendButtons): buttons=0

The output is in the format "device name (debug level:function): actual
message".

Note: if the driver was built with *--disable-debug*, no debug messages
can be printed.
