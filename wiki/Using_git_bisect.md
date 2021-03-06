---
title: Using git bisect
permalink: wiki/Using_git_bisect/
layout: wiki
tags:
 - HOWTO
 - DeveloperPages
---

The **git-bisect** command can be used to narrow down the point at which
a bug was introduced. Pinpointing the fault to a single commit allows
the linuxwacom developers to research the change in detail to understand
(and hopefully fix) its cause.

To perform a git bisect, you must first clone a copy of the relevant
code and install any build dependencies. See the pages on
[xf86-input-wacom](xf86-input-wacom "wikilink"),
[input-wacom](input-wacom "wikilink"), or
[libwacom](libwacom "wikilink") for details.

`$ git clone `<URL>  
`$ cd `<directory>  
`$ git bisect start`

Running the Bisect
------------------

The first step is to find two commits: a "bad" commit which exhibits the
bug and a "good" commit which does not. We'll start with building and
testing the master branch, just to be sure that the bug has not already
been fixed. Use the following commands to build and install/load the
updated code:

**For input-wacom:**

`$ ./autogen.sh`  
`$ make clean`  
`$ rm -f */wacom.ko`  
`$ make`  
`$ sudo modprobe -r wacom`  
`$ sudo insmod */wacom.ko`

**For xf86-input-wacom:**

`$ ./autogen.sh --prefix=`<prefix>` --libdir=`<libdir>  
`$ make`  
`$ sudo make install`  
`[logout / login]`

Once the updated code is running, thoroughly test to see if the bug is
present or not. If the bug isn't present, then you can stop testing at
this point -- the bug has already been fixed and should be available in
the next release. If, however, the bug is present, you will need to run
`git bisect bad` to let git know that this version has issues.

The next step is to find a "good" version of the code. Repeatedly run
`git checkout <version>` and build/test the driver with earlier and
earlier releases. Be sure to run `git bisect bad` if your tests indicate
the bug is present. If for some reason you are unable to test a
particular version (e.g. because a *different* bug prevents you from
testing), you should run `git bisect skip`. Once you find a version
which does not have the bug, you need to run `git bisect good`. At this
point, you should see a message like the following:

`Bisecting: 250 revisions left to test after this (roughly 8 steps)`  
`[3e50218b3e1424efe3f980a94aa5e9aed3c2bc47] HID: wacom: generic: Add support for vendor-defined "Sense" usage`

This indicates that git can now begin zeroing in on the faulty commit.
It will have automatically checked out a new version that could have
possibly introduced the bug. Build and test this driver, and then use
`git bisect bad` or `git bisect good` or `git bisect skip` as
appropriate. Each time you run one of the **git bisect** commands, git
will checkout a new version. The estimated number of steps remaining is
listed each time.

Finishing the Bisect
--------------------

At some point, instead of reporting back that there are more revisions
left to test, the git bisect will end. When this happens, one of two
things can happen. If you never had to skip a commit, you should see a
message stating exactly which commit is buggy:

`4e65a8de49526bf794563ec38bd51e590225d8a0 is the first bad commit`  
`commit 4e65a8de49526bf794563ec38bd51e590225d8a0`  
`Author: Jason Gerecke <killertofu@gmail.com>`  
`Date:   Mon Aug 3 10:18:10 2015 -0700`  
  
`    HID: wacom: Simplify 'wacom_pl_irq'`  
`    `  
`    Unlike other IRQ functions, 'wacom_pl_irq' uses the second element of`  
`    the 'tool' array to store information about its single pen. This makes`  
`    the function more difficult to understand (since it doesn't follow the`  
`    general pattern of other IRQ functions) and prevents the possibility of`  
`    refactoring how pen state is stored.`  
`    `  
`    This patch rewrites 'wacom_pl_irq' to follow the usual IRQ conventions,`  
`    including storing tool type in 'tool[0]' and implicitly tracking prox`  
`    with the 'id[0]' variable.`  
`    `  
`    Signed-off-by: Jason Gerecke <jason.gerecke@wacom.com>`  
`    Signed-off-by: Jiri Kosina <jkosina@suse.cz>`  
`    [jason.gerecke@wacom.com: Imported into input-wacom repository (025bcc1)]`  
`    Signed-off-by: Jason Gerecke <jason.gerecke@wacom.com>`

If you had to skip one or more commits, however, git will likely report
back a message like the following:

`There are only 'skip'ped commits left to test.`  
`The first bad commit could be any of:`  
`bb2d87217207d8127fd73d7f0a92f482320b85e8`  
`4dce7932133ec63acfb547c5e0f4f9d4d68ec2ec`  
`We cannot bisect more!`

In either case, while these final messages are helpful, it is even more
useful to receive a full log of the git bisect process. This will
include information about exactly which commits you tested and wether
your marked them as **bad**, **good**, or **skip**. Once the bisection
process has ended, please run the following and provide us with a copy
of the output:

`$ git bisect log`

Afterwards, it is a good idea to reset your environment in case you need
to run a bisection again later. To do this, run `git bisect reset`.

Tips & Tricks
-------------

Sometimes you may accidentally mark a **good** commit as **bad** or
**skip** a commit you didn't intend. If you end up running the wrong
command, it is possible to recover by first running
`git bisect log > bisect.log` and then editing the generated
**bisect.log** file to match what you had intended. Finally, run
`git bisect reset && git bisect replay bisect.log` to apply the fix.
Once these commands have been run you can continue with building and
testing as normal.
