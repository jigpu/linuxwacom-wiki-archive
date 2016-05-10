---
title: Making Releases
permalink: wiki/Making_Releases/
layout: wiki
tags:
 - MaintainerPages
---

xf86-input-wacom
================

This section describes how to do a new xf86-input-wacom release. VERSION
stands for the new version (e.g. 0.10.3), OLDVERSION for the previous
version (e.g. 0.10.2).

1.  Change configure.ac to reflect the new version number and commit:
    **git commit -sm "wacom VERSION" configure.ac** You *must not* have
    uncommitted changes when releasing a new version.
2.  Run **autoreconf -iv** or **autogen.sh**, build and install the
    driver. Verify that the new version shows in the Xorg.log.
3.  Run **make distcheck**. Verify the name of the tarball
    (xf86-input-wacom-VERSION.tar.bz2). If make distcheck fails, reset
    with **git reset --hard HEAD~1** and resolve the bugs first.
4.  Tag the module: '''git tag -m "xf86-input-wacom VERSION" -s
    xf86-input-wacom-VERSION
5.  Push to the remote: **git push origin master**
6.  Run the release script: **sh release.sh**. This script will push the
    tag and upload the tarball to the remote.
7.  Type up a short announce email summarising the new features in this
    version and append the git shortlog: **git shortlog
    xf86-input-wacom-OLDVERSION..xf86-input-wacom-VERSION**
8.  **Sign off the email** and send it to the [ linuxwacom-announce
    list](/wiki/Mailing_lists "wikilink")
9.  Change the default download on Sourceforge to be the newly-uploaded
    tarball
10. Close all Sourceforge and Github bugs that were fixed in this
    release by setting the status to "closed-fixed" and posting the
    following message: "Fix available in xf86-input-wacom <version>"

Note that it may take a while until the uploaded tarballs are available
through the download links.

libwacom
========

This section describes how to do a new [libwacom](libwacom "wikilink")
release.

1.  Update `NEWS` with an outline of changes since the last version
2.  Change configure.ac to reflect the new version number and commit:
    **git commit -sm "libwacom VERSION" configure.ac NEWS** You *must
    not* have uncommitted changes when releasing a new version.
3.  Run **autoreconf -iv** or **autogen.sh**, build and install the
    library. Verify that *' pkg-config --modversion libwacom*' shows the
    new version
4.  Run **make distcheck**. Verify the name of the tarball
    (libwacom-VERSION.tar.bz2).
5.  Tag the module: '''git tag -m "libwacom VERSION" -s libwacom-VERSION
6.  Push to the remote: **git push origin master** and for the tag **git
    push origin libwacom-VERSION**
7.  Scp the tarball to sourceforge. [Create a
    shell](/wiki/DealingWithSourceforge "wikilink") and then run **scp
    libwacom-VERSION.tar.bz2
    USER@shell.sourceforge.net:/home/frs/project/l/li/linuxwacom/libwacom/**
8.  Generate the git shortlog with **git log --no-merges
    libwacom-0.4..libwacom-0.5 \| git shortlog**, then add the NEWS
    section you added above.
9.  **Sign off the email** and send it to the [ linuxwacom-announce
    list](/wiki/Mailing_lists "wikilink")
10. Close all Sourceforge and Github bugs that were fixed in this
    release by setting the status to "closed-fixed" and posting the
    following message: "Fix available in libwacom <version>"

Note that it may take a while until the uploaded tarballs are available
through the download links.

input-wacom
===========

This section describes how to do a new
[input-wacom](input-wacom "wikilink") release.

1.  Change configure.ac to reflect the new version number and commit:
    **git commit -sm "input-wacom VERSION" configure.ac** You *must not*
    have uncommitted changes when releasing a new version.
2.  Tag the module: **git tag -m "input-wacom VERSION" -s
    input-wacom-VERSION**. You will use your pgp key for this step.
3.  Run **make dist**. Verify the name of the tarball
    (input-wacom-VERSION.tar.bz2). If this is a clean clone of the
    repository, you will need to first run autogen.sh to generate the
    Makefile.
4.  Build, install, and load the driver from the tarball.
5.  Verify the version number of the loaded driver with \`modinfo wacom
    \| grep version\`.
6.  Push to the remote: **git push origin master**.
7.  Run the release script: **sh release.sh** with the correct arguments
    (--user <username>@ <path>). This script will push the tag and
    upload the tarball to the remote.
8.  Type up a short announcement summarizing the new features in this
    version and insert it in the generated .announce email file (the git
    shortlog).
9.  **Sign off the email** and send it to the [ linuxwacom-announce
    list](/wiki/Mailing_lists "wikilink") using git send-email.
10. Close all Sourceforge and Github bugs that were fixed in this
    release ***and** which are fixed in an upstream Linux release* by
    setting the status to "closed-fixed" and posting the following
    message: "Fix available in Linux <version> and input-wacom
    <version>"
