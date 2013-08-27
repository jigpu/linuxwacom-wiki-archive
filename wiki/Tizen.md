---
title: Tizen
permalink: wiki/Tizen/
layout: wiki
---

[Tizen](https://www.tizen.org/) is a project backed by Samsung and Intel
which seeks to define a Linux-based platform for mobile devices. It is
the successor in many ways to earlier projects such as MeeGo and LiMo.

While the Tizen platform guarantees nothing more than a set of standard
libraries, when looking under the hood one finds a fairly standard Linux
distribution. Usually this consists of a custom UI built with the
[Enlightenment Foundation
Libraries](http://www.enlightenment.org/?p=about/efl) running on an X
server. Other familiar tools like systemd, udev, and a host of GNU
utilities are available.

Because Tizen is so similar to other Linux distributions under the hood,
the standard kernel and X11 drivers can be used to provide support for
Wacom digitizers. While Tizen can RPMs packages, there are a few minor
dependency hiccups preventing you from just downloading and installing a
package from Red Hat. Instead, it is necessary to build an RPM from
source.

Tizen uses a tool called
"[GBS](https://source.tizen.org/documentation/reference/git-build-system)"
(Git Build System) to build packages from source. It requires a git
tree, so you'll need to checkout xf86-input-wacom from our repository.
Also, because it is building an RPM, you'll need to supply a spec file;
Google around for one (small changes may be necessary to work around
build errors). Finally, since GBS sets up a clean chroot to do the build
in (for obvious reasons it can't use your system libraries and headers)
you'll need to supply it with a location that it can find RPMs for any
build dependencies. The `~/.gbs.conf` config file comes with some
presets; these may need to be modified if you are building a different
version or for a different architecture.

Once everything is set up, GBS can do its thing. The following should
kick off the build:

`   $ gbs build -A `<Arch>` --include-all xf86-input-wacom`

As mentioned above, it may take some trial-and-error to get the build
system happy with the spec file and RPM repositories. However, once are
both set up properly the builds should occur without a hitch.

After the RPM is built, you can either install it directly onto the
target device via the `rpm` command, or (re)build an image to flash onto
the target device. In the case of the latter, you will want to study up
on
"[MIC](https://source.tizen.org/documentation/articles/mic-image-creator)"
(the MIC Image Creator) and kickstart files. You can add the repository
created by GBS to your kickstart file, as well as the xf86-input-wacom
package.
