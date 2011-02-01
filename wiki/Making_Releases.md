---
title: Making Releases
permalink: wiki/Making_Releases/
layout: wiki
tags:
 - MaintainerPages
---

This page describes how to do a new xf86-input-wacom release. VERSION
stands for the new version (e.g. 0.10.3), OLDVERSION for the previous
version (e.g. 0.10.2).

1.  Change configure.ac to reflect the new version number and commit:
    **git commit -sm "wacom VERSION" configure.ac** You *must not* have
    uncommitted changes when releasing a new version.
2.  Run **autoreconf -iv** or **autogen.sh**, build and install the
    driver. Verify that the new version shows in the Xorg.log.
3.  Run **make distcheck**. Verify the name of the tarballs
    (xf86-input-wacom-VERSION.tar.bz2 and
    xf86-input-wacom-VERSION.tar.gz). If make distcheck fails, reset
    with **git reset --hard HEAD~1** and resolve the bugs first.
4.  Tag the module: '''git tag -m "xf86-input-wacom VERSION" -s
    xf86-input-wacom-VERSION
5.  Push to the remote: **git push origin master**
6.  Run the release script: **sh release.sh**. This script will push the
    tag and upload the tarballs to the remote.
7.  Type up a short announce email summarising the new features in this
    version and append the git shortlog: **git shortlog
    xf86-input-wacom-OLDVERSION..xf86-input-wacom-VERSION**
8.  **Sign off the email** and send it to the [ linuxwacom-announce
    list](/wiki/Mailing_lists "wikilink")

Note that it may take a while until the uploaded tarballs are available
through the download links.
