---
title: Gestures
permalink: wiki/Gestures/
layout: wiki
---

Gestures for all wacom tablets are not currently supported. The reason
for this is, in order to be supported and be supported decently by a
wide range of programs gestures need to be implemented in Xorg itself.

The issue is if linuxwacom or xf86-input-wacom added gestures then a
gesture that might scroll in one program would do a completely different
and unwanted action in another program. While this could be implemented
it could possibly create a very large hassle in the end.

For discussion of gestures (and inherently multi-touch) on the
Xorg-devel mailing list please see the following threads:
<http://lists.x.org/archives/xorg-devel/2010-March/006388.html>
<http://lists.x.org/archives/xorg-devel/2010-March/006206.html>
