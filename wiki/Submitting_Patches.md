---
title: Submitting Patches
permalink: wiki/Submitting_Patches/
layout: wiki
tags:
 - DeveloperPages
---

A patch that is suitable for merging has to meet a number of formal
requirements:

-   it must be a [ git-formatted patch](/wiki/Using_Git "wikilink")
-   it must be signed off
-   it must have a single-line subject (less than 72 characters)
-   it must have a meaningful commit message
-   The driver must pass [*make check*](/wiki/TestSuite "wikilink")

Any patch that meets these requirements can be sent to the
[linuxwacom-devel](https://lists.sourceforge.net/lists/listinfo/linuxwacom-devel)
mailing list for review and merging. Below is more information on each
of these three points.

Signing off your patch
----------------------

A *Signed-off-by:* tag in your commit message certifies that you wrote
it or otherwise have the right to pass it on as a open-source patch. For
more information on this tag see [the kernel's
Documentation/SubmittingPatches](http://lxr.linux.no/linux/Documentation/SubmittingPatches)
guidelines.

Commit message format
---------------------

The commit message format follows the following style:

    One line summary less than 72 characters
       (a single blank line)
    A multi-line or multi-paragraph message describing the change in more 
    detail and, most of all, describes your motivation for this patch.

    If you want to, you can include compiler output or statistics here too.

As always when coding, common sense is the most important rule and there
are patches where a single line is enough or other rules apply. The goal
of a commit message is always to make others quickly aware of your
motivation and what you did so they can review the code with this in
mind.

Submitting and review
---------------------

Once submitted, it may take several days until a review comes in. In the
best case, your patch went straight in. Many patches however need to be
re-done before they are merged. Don't take it badly, even the
maintainers of the driver regularly re-do the patches after review on
the list. The main goal is to have an easy to understand, bug-free,
maintainable codebase.

If you have to re-submit a patch, please state that this is a new
version of a previous patch and list the differences to the previous
version so reviewers can concentrate on that. The best space for this is
after the **--** in the patch file. The part there is automatically
removed when applying the patch. And example is

    [PATCH v2] the meaningful subject line

    most likely same summary as before but may be extended or changed. either way,
    still a useful summary.

    Signed-off-by: Wile E. Coyote <customer-no-1@acme.com>
    ---

    Changes to previous version:
    - fixed spelling of typo
    - changed the world, is now flat again
    - removed universe, world is center now

      geography/world.c                     | 5000 ++++---
      geography/universe.c                  | 5000 -------

    [...]
