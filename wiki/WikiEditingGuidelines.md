---
title: WikiEditingGuidelines
permalink: wiki/WikiEditingGuidelines/
layout: wiki
---

This page lists a set of guidelines for how to edit the wiki to keep it
informative and easy-to navigate.

How to get involved
===================

In order to edit this wiki, you need a [sourceforge
account](https://sourceforge.net/user/registration) and notify the
[Maintainers](/wiki/Maintainers "wikilink") of this project to [give you
editor rights](/wiki/AddingWikiUsers "wikilink") on the wiki.

What to add?
============

We use a wiki to make it easy to add and maintain information on the
wiki. But information on websites goes stale fast when the codebase
moves (and the wacom drivers move fast). Stale information means users
won't find what they need or what they find won't work. And that defeats
the purpose of having the wiki. Be conservative in what you add and make
sure that the information is correct and will remain correct for the
immediate future.

What not to add?
================

-   Do not add hacks or other tricks that happen to work on your
    computer. Chances are high the hack isn't upstream because it only
    works on a few devices, will stop working soon or is the wrong
    approach altogether.
-   Extensive parameter/option documentation: this belongs into man
    pages so it can be updated when the code updates.

Use of Categories
=================

mediawiki has a feature called
[Categories](http://www.mediawiki.org/wiki/Help:Categories) to group
pages. Each page can be part of zero, one or more categories. We
currently use the following categories:

-   [Category%3AHOWTO](/wiki/Category%3AHOWTO "wikilink"): for any HOWTO's s
    related to the driver setup
-   [Category%3ADeveloperPages](/wiki/Category%3ADeveloperPages "wikilink"):
    for anything related to development
-   [Category%3AMaintainerPages](/wiki/Category%3AMaintainerPages "wikilink"):
    for anything related to maintainership of the project
-   [Category%3ALinuxwacom](/wiki/Category%3ALinuxwacom "wikilink"): for
    anything that is related to [linuxwacom](linuxwacom "wikilink")
    only.

If you are adding or editing a page, please make sure it is part of the
appropriate category.

On adding code to this wiki
===========================

Do not add code to this wiki. This includes scripts as well as actual
source code. Send the code snippets to the [linuxwacom-devel mailing
list](mailing_lists "wikilink") first so it can be reviewed by a
developer and only then add it.

Many scripts and workarounds only work on certain combinations of the
driver, a kernel, and X server. Adding a script to this website without
taking this into account means users will not find the right solution if
they don't have that exact same solution.

On external programs
====================

This wiki is not a wiki for [external
programs](/wiki/External_applications "wikilink"). If an external program is
required or useful for configuration, feel free to add the
wacom-specific part to this wiki. Do not add build instructions for the
external program but instead link to the project-specific page where
these build instructions are found. If the project doesn't have any,
contact the developers of that project to add it to their website.
External programs change and wiki content can go stale quickly.

Always add the date and the version of the software you have been been
using so users know how old information is.

Sections that need work
=======================

The [Category%3AFixme](/wiki/Category%3AFixme "wikilink") page has a list of
all pages that require extra work.
