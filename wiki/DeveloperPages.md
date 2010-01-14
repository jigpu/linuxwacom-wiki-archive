---
title: DeveloperPages
permalink: wiki/DeveloperPages/
layout: wiki
---

Making releases
===============

This section describes how to do a new xf86-input-wacom release. VERSION
stands for the new version (e.g. 0.10.3), OLDVERSION for the previous
version (e.g. 0.10.2).

1.  Change configure.ac to reflect the new version number and commit:
    **git commit -sm "wacom VERSION" configure.ac** You *must not* have
    uncommitted changes when releasing a new version.
2.  Run **autoreconf -iv** or **autogen.sh**, build and install the
    driver. Verify that the new version shows in the Xorg.log.
3.  Run **make distcheck**. Verify the name of the tarballs
    (xf86-input-wacom-VERSION.tar.bz2 and
    xf86-input-wacom-VERSION.tar.gz). If make distcheck
4.  Tag the module: '''git tag -m "xf86-input-wacom VERSION" -s
    xf86-input-wacom-VERSION
5.  Push to the remote: **git push origin master**
6.  Push the tag to the remote **git push origin
    xf86-input-wacom-VERSION**
