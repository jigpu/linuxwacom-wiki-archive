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
    xf86-input-wacom-VERSION.tar.gz). If make distcheck fails, reset
    with **git reset --hard HEAD~1** and resolve the bugs first.
4.  Tag the module: '''git tag -m "xf86-input-wacom VERSION" -s
    xf86-input-wacom-VERSION
5.  Push to the remote: **git push origin master**
6.  Push the tag to the remote: **git push origin
    xf86-input-wacom-VERSION**
7.  Download
    [xf86-input-wacom-md5](https://sourceforge.net/projects/linuxwacom/files/xf86-input-wacom/xf86-input-wacom.md5/download)
    and append the checksums for the new tarballs with: **md5sum
    xf86-input-wacom-VERSION.tar\* &gt;&gt; xf86-input-wacom.md5**.
8.  Upload the .md5, the .tar.gz and the .tar.bz2 file *in this order*
    to the [sourceforge file
    explorer](https://sourceforge.net/project/admin/explorer.php?group_id=69596).
    The order ensures that the tar.bz2 file is the newest and offered by
    the big download button. To upload, click on the gear icon next to
    the xf86-input-wacom folder and click "upload here". If you upload
    to the wrong folder, you can cut/paste with the gear icon next to
    the uploaded file.
9.  Type up a short announce email summarising the new features in this
    version and append the git shortlog: **git shortlog
    xf86-input-wacom-OLDVERSION..xf86-input-wacom-VERSION**
10. **Sign off the email** and send it to the [ linuxwacom-announce
    list](/wiki/Mailing_lists "wikilink")
