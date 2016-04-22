---
title: Input-wacom backports
permalink: wiki/Input-wacom_backports/
layout: wiki
tags:
 - DeveloperPages
---

Backporting commits into input-wacom
------------------------------------

The "input-wacom" driver is a backported version of the upstream Wacom
kernel drivers which allows users who are running older kernels to
benefit from the latest updates. As patches are accepted upstream, it is
necessary to ensure they are also added to input-wacom so the codebases
stay in sync. This page outlines the steps so that the process can be
followed without having to commit it to memory.

### Prerequisites

Before beginning, you'll need to be sure your development machine has a
copy of \`git\` installed and that you have clones of both the Linux
kernel repository and our own input-wacom repository. Because
development will not typically take place on Linus' upstream repository,
additional "remotes" will need to be added that point to active
development trees. The two remotes we deal most often with are Jiri's
(HID subsystem) and Dmitry's (Input subsystem).

<code>

`(kernel.git)~ git remote add jiri `[`https://git.kernel.org/pub/scm/linux/kernel/git/jikos/hid.git`](https://git.kernel.org/pub/scm/linux/kernel/git/jikos/hid.git)  
`(kernel.git)~ git remote add dtor `[`https://git.kernel.org/pub/scm/linux/kernel/git/dtor/input.git`](https://git.kernel.org/pub/scm/linux/kernel/git/dtor/input.git)

</code>

In addition, to keep [our Github
mirror](https://github.com/linuxwacom/input-wacom) in sync with
Sourceforge, it will need to be added as a "remote" of the input-wacom
repository as well. Furthermore, if you would like the full benefit of
our continuous integration tests, it is recommended to fork the Github
repository and then [sign up for a Travis-CI
account](https://travis-ci.org/). Once the fork has been made and the
repository enabled in Travis-CI, you can add it as a remote as well:

<code>

`(input-wacom.git)~ git remote add mirror `[`https://github.com/linuxwacom/input-wacom.git`](https://github.com/linuxwacom/input-wacom.git)  
`(input-wacom.git)~ git remote add fork `[`https://github.com/${USERNAME}/input-wacom.git`](https://github.com/$%7BUSERNAME%7D/input-wacom.git)

</code>

The remainder of this document assumes that the remotes "jiri", "dtor",
"mirror", and "fork" exist as described above. If you choose different
names, please substitute them where necessary.

### Importing Upstream Commits

**Step 1:** The first step is to navigate to your upstream kernel
directory and have it update its remotes so that you can have access to
the latest commits. The output of this command lists the branches which
have been updated; you can use this information to determine if anything
interesting may need to be imported into the input-wacom repository. As
can be seen in the sample output below, there have been updates to
"jiri/for-4.6/wacom", "for-4.6/upstream-fixes", etc. Of particular
interest are Jiri's "wacom", "upstream", and "upstream-fixes" branches
since these will typically be where he pushes updates to.

<code>

`(kernel.git)~ git remote update`  
`Fetching origin`  
`remote: Counting objects: 319561, done.`  
`remote: Compressing objects: 100% (76364/76364), done.`  
`remote: Total 319561 (delta 251640), reused 296046 (delta 242417)`  
`Receiving objects: 100% (319561/319561), 144.47 MiB | 2.51 MiB/s, done.`  
`Resolving deltas: 100% (251640/251640), done.`  
`From `[`git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux`](git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux)  
`   b562e44..d3436a1  master     -> origin/master`  
` * [new tag]         v4.5       -> v4.5`  
` * [new tag]         v4.5-rc1   -> v4.5-rc1`  
` * [new tag]         v4.5-rc2   -> v4.5-rc2`  
` * [new tag]         v4.5-rc3   -> v4.5-rc3`  
` * [new tag]         v4.5-rc4   -> v4.5-rc4`  
` * [new tag]         v4.5-rc5   -> v4.5-rc5`  
` * [new tag]         v4.5-rc6   -> v4.5-rc6`  
` * [new tag]         v4.5-rc7   -> v4.5-rc7`  
` * [new tag]         v4.6-rc1   -> v4.6-rc1`  
` * [new tag]         v4.6-rc2   -> v4.6-rc2`  
`Fetching jiri`  
`remote: Counting objects: 149, done.`  
`remote: Compressing objects: 100% (149/149), done.`  
`remote: Total 149 (delta 98), reused 0 (delta 0)`  
`Receiving objects: 100% (149/149), 147.53 KiB | 0 bytes/s, done.`  
`Resolving deltas: 100% (98/98), done.`  
`From `[`git://git.kernel.org/pub/scm/linux/kernel/git/jikos/hid`](git://git.kernel.org/pub/scm/linux/kernel/git/jikos/hid)  
` * [new branch]      for-4.5/upstream -> jiri/for-4.5/upstream`  
` * [new branch]      for-4.5/upstream-fixes -> jiri/for-4.5/upstream-fixes`  
` * [new branch]      for-4.6/cmedia -> jiri/for-4.6/cmedia`  
` * [new branch]      for-4.6/i2c-hid -> jiri/for-4.6/i2c-hid`  
` * [new branch]      for-4.6/logitech -> jiri/for-4.6/logitech`  
` * [new branch]      for-4.6/multitouch -> jiri/for-4.6/multitouch`  
` * [new branch]      for-4.6/penmount -> jiri/for-4.6/penmount`  
` * [new branch]      for-4.6/sony -> jiri/for-4.6/sony`  
` * [new branch]      for-4.6/thingm -> jiri/for-4.6/thingm`  
` * [new branch]      for-4.6/upstream -> jiri/for-4.6/upstream`  
` * [new branch]      for-4.6/upstream-fixes -> jiri/for-4.6/upstream-fixes`  
` * [new branch]      for-4.6/wacom -> jiri/for-4.6/wacom`  
`   f212bd9..e1c9b9f  for-linus  -> jiri/for-linus`  
`   369032a..ab99ccb  for-next   -> jiri/for-next`  
`   851328f..d66435c  master     -> jiri/master`

</code>

**Step 2:** Now that you've updated the kernel repository and are aware
of which branches have updates, it is time to determine the last commit
that has appeared in each relevant input-wacom branch. Since there were
updates to "jiri/for-4.6/wacom" and "for-4.6/upstream-fixes", lets look
at the corresponding "jiri/for-4.6" branch in input-wacom. The last
commit should have a note stating something like
"\[jason.gerecke@wacom.com: Imported into input-wacom repository
(f620516)\]" -- the final hex value in this line indicates what upstream
commit this was imported from.

**Step 3:** Now that we know the last upstream commit that was imported
into our branch of interest, we can export all the more recent commits
from the kernel repository. For example, the command below would produce
a list of patches produced between f620516 and Jiri's "for-4.6/wacom"
branch that changed one of the `drivers/hid/wacom*` files. When
exporting patches made to the w8001 driver under Dmitry's care, you'll
need to change this last argument to
`drivers/input/tablet/wacom* drivers/input/touchscreen/wacom*`.

<code>

`(kernel.git)~ git format-patch f620516..jiri/for-4.6/wacom -- drivers/hid/wacom*`  
`# No output`

</code>

Since nothing was printed out, that means that none of the new upstream
commits in "jiri/for-4.6/wacom" affected our driver. Next, lets do the
same thing, but for the "jiri/for-4.6/upstream-fixes" branch:

<code>

`(kernel.git)~ git format-patch f620516..jiri/for-4.6/upstream-fixes -- drivers/hid/wacom*`  
`0001-HID-wacom-fix-Bamboo-ONE-oops.patch`  
`0002-HID-wacom-Initialize-hid_data.inputmode-to-1.patch`  
`0003-HID-wacom-Support-switching-from-vendor-defined-devi.patch`

</code>

This time, we can see that there are three commits that Jiri applied
which affected our driver which will have to be added to input-wacom.

**Step 4:** With the commits that need to be imported now available as
.patch files, we need to make some small edits to them before importing
them. Firstly, we need to change the path to coincide with the path used
by input-wacom:

<code>

`(kernel.git)~ sed -i 's!drivers/hid/!3.17/!' *.patch`

</code>

Secondly, we need to edit the files to add in the "Imported" message and
a new "Signed-off-by" line to the end of the commit message. When
writing the "Imported" message, the first 7 characters of the commit ID
(listed in full on the first line of the patch file) should be used.

**Step 5:** At this point the files are ready and you can attempt to
import them into the input-wacom repository:

<code>

`(input-wacom.git)~ git am ${KERNELDIR}/*.patch`

</code>

If a merge error occurs during the import process, git will suggest
trying to apply the patch manually (e.g. with \`patch -p1 &lt;
.git/rebase-apply/patch\`). Go ahead and try this, and then resolve any
patch errors by looking at the generated ".rej" files. Commit the fixed
version, and then continue applying patches by running
`git am --continue`.

**Step 6:** With all the patches successfully imported, it is now time
to test them. Run \`./configure && make\` to verify everything compiles,
\`sudo modprobe -r wacom && insmod 3.17/wacom.ko\` to test things out.
If any errors are encountered, you'll need to edit the buggy commit with
the necessary changes.

**Step 7:** Once you've completed importing the patches from one
upstream branch, you can move on to the next until you've imported
patches from all upstream branches.

**Step 8:** After everything has been imported, the next step is to go
through the patches and see if any need to be backported. If they do,
you can continue with the steps below.

### Backporting Patches to Earlier Kernels

TBD.
