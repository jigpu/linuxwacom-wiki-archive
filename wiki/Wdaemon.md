---
title: Wdaemon
permalink: wiki/Wdaemon/
layout: wiki
tags:
 - HOWTO
---

[wdaemon](wdaemon "wikilink") is a daemon to hide physical tablet
unplugging. When it was originally written, the X server did not have
input device hotplugging support. Input devices added at runtime could
thus not be used. wdaemon worked around this by providing a permanent
virtual kernel device that looked exactly like the physical tablet to
userspace. The X server was then configured to use the wdaemon device.
Whenever a physical tablet was plugged in, wdaemon would then forward
the events from this device through the virtual device.

Now the X server *does* support input device hotplugging. From a
technical point of view, wdaemon works exactly as before. From a
practical perspective however it works the other way round - it
*disables* device hotplugging. A device that would otherwise appear and
disappear as X input device now looks like a permanent device to X.
Thus, run-time settings applied to this device remain permanent.

Note that the virtual device created by wdaemon will still be hotplugged
by the X server (if so configured), hence wdaemon provides a two-layer
hotplug abstraction.

Getting wdaemon
===============

The git repository is available at

` `[`git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/wdaemon`](git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/wdaemon)

wdaemon can be built and installed with the usual

`  ./autogen.sh --prefix=/usr`  
`  make && make install`
