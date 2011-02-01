---
title: Using Git
permalink: wiki/Using_Git/
layout: wiki
tags:
 - DeveloperPages
---

This page gives you a very basic outline of the git commands you will
need to get the driver, hack and commit as well as to generate patches
for submission. Treat this page as a quick reference only, it is
strongly recommended that you peruse the links provided at the end of
the page for a better understanding of git commands.

Getting the latest source
-------------------------

` git clone `[`git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom`](git://linuxwacom.git.sourceforge.net/gitroot/linuxwacom/xf86-input-wacom)

This gets the latest upstream source and clones it into a
*xf86-input-wacom* directory. If you already have a checked-out version,
we recommend that you do a rebasing pull to update.

` git pull --rebase`  
` # if conflicts occur during rebase, resolve them and run`  
` git add conflicting-file.c`  
` git add other-conflicting-file.h`  
` git rebase --continue`

If you have no local changes, you will only ever need the pull command.

If you want to work on a branch other than the default *master* branch,
use

` git checkout -b branchname origin/branchname`

This will create a local branch *branchname* and set it up to track the
origin's branch of the same name. Note that if you are working with
branches, we really encourage you to get familiar with git first.

Hacking the code
----------------

During hacking, the two commands you will most likely need are *diff*
and *checkout*:

` git diff`

to show what has changed.

If you want to reset the state to the last commit, run:

` git checkout filename.c`

or (for all files)

` git checkout -f`

Note that this throws all uncommitted changes away and you can't get
them back again.

Committing
----------

Once you have changed the code to your liking, commit. The simplest way
to do so is to commit everything with

` git commit -as`

We recommend to commit early and often and reduce the content of a
commit to a single *logical* change. It is better to err on the side of
too many commits, git makes it quite easy to squash two commits
together. Git allows partial commits of the code, i.e. if you have made
several changes to the code you can commit some hunks only.

` git add -p`  
` # follow the prompt to add the matching hunks`  
` git commit -s`  
` # write a proper commit message`

Repeat the steps above until everything is committed. Note that if you
do commit partially, ensure that each commit builds separately. All
commits that you intend to submit must be singed off (see
[SubmittingPatches](/wiki/SubmittingPatches "wikilink")).

Creating a patch for submission
-------------------------------

` git format-patch HEAD~1`

To create a patch from the very last commit. Or, if you have multiple
commits, create a numbered patchset. Example for the last 7 commits:

` git format-patch -n HEAD~7`

These patches are now ready for [
submission](/wiki/SubmittingPatches "wikilink").

Recommended reading
-------------------

-   <http://who-t.blogspot.com/2009/12/on-commit-messages.html> A guide
    to writing commit messages
-   <http://www.kernel.org/pub/software/scm/git/docs/v1.2.6/tutorial.html>
    An excellent introduction to git can be found
-   <http://www.freedesktop.org/wiki/Infrastructure/git/Developers>
    freedesktop.org's git tutorial provides the same use-cases that
    apply to xf86-input-wacom development.
