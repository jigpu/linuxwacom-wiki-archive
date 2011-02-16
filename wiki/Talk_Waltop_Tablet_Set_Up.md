---
title: Talk:Waltop Tablet Set Up
permalink: wiki/Talk:Waltop_Tablet_Set_Up/
layout: wiki
---

2-15-11 So far Waltop's only need to configure the stylus and two stylus
buttons. Didn't see the point in overloading them with a lot of
non-relevant Wacom script sections.

Defaults are used on all the scripts except for pad. The idea is a text
file based framework to in effect duplicate wacomcpl's gui.

The only issue the Waltops are having with with 2.6.35, Xorg 1.9, and
xf86-input-wacom 0.10.8 is both stylus buttons have the same value.
xsetwacom does not allow them to set one to right click and the other to
middle click. They're both right click or middle click. The very
earliest reporters said they could, so this may be a kernel issue
(kernel update). - Favux
