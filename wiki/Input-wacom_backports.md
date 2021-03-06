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

**Step 1:** Navigate to your upstream kernel directory and fetch all
upstream updates for the remotes. As git downloads the new commits, it
will print out information about new branches and branches which have
updates. New code which affects our driver could potentially be
contained in any updated branch, but typically updates will only occur
in Jiri's "wacom", "upstream", and "upstream-fixes" branches (as well as
similar branches owned by Dmitry). Additionally, it is a good idea to
periodically also check Linus' RC tags to ensure they don't contain any
unexpected code.

In the sample below, we fetch updates for all upstream repositories by
running \`git remote update\`. The output lists a number of branches of
interest: "jiri/for-4.5/upstream", "jiri/for-4.5/upstream-fixes",
"jiri/for-4.6/upstream", "jiri/for-4.6/upstream-fixes", and
"jiri/for-4.6/wacom". Additionally, there are new RCs tagged by Linus
that could also be investigated.

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

**Step 2:** For each branch or tag of interest from step 1, you will
need to see if they contain any new commits that have not already been
added to the input-wacom repository. The easiest way of doing this is to
look at the last commit in each of input-wacom's upstream mirror
branches. For example, we might find that the commit message of the last
commit to our "jiri/for-4.5" branch contains the message
"\[jason.gerecke@wacom.com: Imported into input-wacom repository
(0bbfe28)\]". This indicates that the branch should contain all commits
prior to 0bbfe28 and that anything newer may need to be added.

For each branch that may have new upstream commits (e.g., "jiri/for-4.5"
and "jiri/for-4.6") take note of the commit ID mentioned in the last
commit message.

**Step 3:** With the information gathered in step 2, we can have git
export all commits newer than than that commit which affect our driver.
To do this, we can run a command similar to the following example. The
command should be run for each branch that may have new commits on it,
replacing the commit SHA with the SHA from step 2 and the branch name
with the branch of interest. The final path argument will depend on the
remote -- for Jiri's branches you will want to use
"drivers/hid/wacom\*", while for Dmitry's use
"drivers/input/touchscreen/wacom\*".

<code>

`(kernel.git)~ git format-patch 0bbfe28..jiri/for-4.5/upstream -- drivers/hid/wacom*`  
`# No output.`

</code>

If nothing is printed out, that means that none of the new upstream
commits to "jiri/for-4.5/upstream" directly affect our driver. In such a
case you can run the command again for the next branch of interest. If
something is printed out, however, proceed to step 4 to integrate the
patches into input-wacom.

<code>

`(kernel.git)~ git format-patch 0bbfe28..jiri/for-4.5/upstream-fixes -- drivers/hid/wacom*`  
`# No output.`  
`(kernel.git)~ git format-patch f620516..jiri/for-4.6/wacom -- drivers/hid/wacom*`  
`# No output.`  
`(kernel.git)~ git format-patch f620516..jiri/for-4.6/upstream -- drivers/hid/wacom*`  
`# No output.`  
`(kernel.git)~ git format-patch f620516..jiri/for-4.6/upstream-fixes -- drivers/hid/wacom*`  
`0001-HID-wacom-fix-Bamboo-ONE-oops.patch`  
`0002-HID-wacom-Initialize-hid_data.inputmode-to-1.patch`  
`0003-HID-wacom-Support-switching-from-vendor-defined-devi.patch`

</code>

After checking several branches, each with no commits of interest, we
see that 3 patches were generated for the "jiri/for-4.6/upstream-fixes"
branch. We should now proceed to step 4 to integrate these patches into
input-wacom.

**Step 4:** The patch files generated in step 3 will need to be slightly
modified before importing them into the input-wacom repository. Firstly,
we need to change the path to coincide with the path used by
input-wacom:

<code>

`(kernel.git)~ sed -i 's!drivers/hid/!3.17/!' *.patch`

</code> or <code>

`(kernel.git)~ sed -i 's!drivers/input/touchscreen!3.17!' *.patch`

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
trying to apply the patch manually (e.g. with
`patch -p1 < .git/rebase-apply/patch`). Go ahead and try this, and then
resolve any patch errors by looking at the generated ".rej" files.
Commit the fixed version, and then continue applying patches by running
`git am --continue`.

**Step 6:** With all the patches successfully imported, it is now time
to test them. Run \`./configure && make\` to verify everything compiles,
\`sudo modprobe -r wacom && sudo insmod 3.17/wacom.ko\` to test things
out. If any errors are encountered, you'll need to edit the buggy commit
with the necessary changes.

**Step 7:** Once you've completed importing the patches from one
upstream branch, you can remove the patch files from the kernel
directory and return to step 3 with the next branch of interest. In this
particular example, "jiri/for-4.6/upstream-fixes" was the last branch of
interest so there was no need to return to step 3.

**Step 8:** Post your changes to the Linux Wacom developer list and push
your changes with, for example:

<code>

`~ git push fork dtor47:dtor/for-4.7`  
`~ git push mirror dtor47:dtor/for-4.7`  
`~ git push origin dtor47:dtor/for-4.7`

</code>

**Step 9:** After all the upstream changes have been imported, the next
step is to go see if any of them need to be backported to earlier kernel
versions (e.g. 3.7, 2.6.38, etc.). Determining if a particular change
needs to be backported will require an understanding of whether the
change fixes a bug or adds a feature that is applicable to older kernel
versions. Any backported changes should be added to the input-wacom
"master" branch rather than one of the upstream mirror branches like
"jiri/for-4.6".

### Creating New input-wacom Branches

To accomplish the above you will need to create new branches when new
branches are created at the (jiri, dtor, etc) kernel remotes. To do
this, merge master on to the current (soon to be old) import branch (eg
jiri/for-4.5). Then create a new import branch from the current (now
'old') import branch. Then push those branches up to origin.

`$ git merge master`  
`$ git branch j46`  
`$ git push origin j45:jiri/for-4.5`  
`$ git push origin j46:jiri/for-4.6`  

Also merge the 'old' current branch (eg j45) into master.

`$ git checkout master` `$ git merge j45`

### Backporting Patches to Earlier Kernels

Backporting requires an understanding of the history of the driver, so
it is not as step-by-step as the importing to 3.17 done above.

As of May 2016, 3.17 is the newest kernel driver in input-wacom. There
are 4 older kernel drivers to backport patches to. These older kernel
drivers are are 3.7, 2.6.38. 2.6.36, and 2.6.30. 3.7 and 2.6.38 are
closer to the 3.17 driver while 2.6.36 and 2.6.30 have been diverged
from.

Whereas imports to the newest driver (3.17) are done on the
(jiri/dtor)/for-x.x branches, backports to the older kernels are done on
master. To begin, find all of the existing patches on the jiri/dtor
branches (see above) which have not been merged into master. Our job
will be to determine how, and if, to 'backport' (or replicate these
changes) for the older kernels.

The following types of changes will be encountered: new features, new
devices, bug fixes, upstream code refactoring, and upstream code
cleanup. In general, we only backport new features, new devices and bug
fixes which are relevant to the older kernels. Upstream code refactoring
and cleanup are considered on a case-by-case basis.

Some bits of knowledge about backporting
----------------------------------------

Kernel 2.6.36 has a transitional touch system. Patches for new devices
which have touch are mostly not backported to this branch.
