# SBP-Man

Python wrapper around aurutils for easy managing a personal repo on cloud.

SBP-Man stands for **S**elf **B**uilt **P**ackage **Man**ager.

# About

Archlinux's biggest strength is the simplicity of package management.
The Archlinux Package Manager (`pacman`) is pre-configured to fetch tarballs from
official Archlinux repositories. In my experience; among binary based linux
distros; Archlinux is superior in it's offering of packages.

Sometimes the repos are lacking in their selection of packages.
There is the other strength of Archlinux; the Arch User Repository (AUR).
The AUR allows Archlinux users to copy the build scripts that other users wrote
(usually through trial and error)
and build packages for themselves so that they can install.

Most Arch users use AUR managers, which there are a ton.
Most AUR managers are trying to copy the workflow of pacman;
ignoring the fact that **building packages** (what an AUR manager *should* do)
is fundamentally different than **unpacking tarballs** (what `pacman` does).

Although initial learning curve is great; the best AUR manager IMO is
[aurutils](https://github.com/AladW/aurutils).
Instead of emulating pacman, `aurutils` packages packages (from AUR) and the end
goal is to put them in a (usually local) repository; so that `pacman` can install
the files.

Couple years ago I ran into a blog post by 
[mdaffin](https://disconnected.systems/blog/archlinux-repo-in-aws-bucket/)
which changed how I approach Archlinux.
I built a similar workflow described in the blogpost
where I have local PKGBUILD's that centralize my arch installation,
and whenever I build packages, I push them into my own AWS bucket.

There is no automation; and bash scripting the process is a bit tedious because
AWS commandline tools are a bit lacking.
For example; it's not really possible to send a package that has `+` in it's
name because the AWS API recognizes it as an invalid key.
Or it is not straightforward to clean up old packages based on naming.
My biggest gripe currently is; VCS packages do not get version bumps due to
upstream changes; they only get bumped when maintainers of the PKGBUILDS make
changes to the pkgbuilds. Thus; if you need a dev build of a package; you need
to constantly monitor upstream; and remove then rebuild the VCS PKGBUILD.

Although I am trying to migrate to a source-based distro; I decided to still
make this wrapper around `aurutils` so that building and deploying to cloud repo
can be automated.
Also, this is my pet python project to learn more about package management.

# Installation

This is a WIP; but eventually a `setup.py` or a `MAKEFILE` will make it's way
into the repo.

# Configuration

The configuration file will be at, preferentially;

* `$XDG_CONFIG_HOME/sbpman/sbpman.conf`
* `~/.config/sbpman/sbpman.conf`
* `~/.sbpman.conf`

The following info will be needed in there;

* Local PKGBUILDs location
* Local chroot location for clean builds
* Local system repo location
* Cloud services type and location
* Cloud services credentials file

Also, depending on the cloud service, some credentials will need to be configured.

# Examples

The intended workflow is;

* `sbpman build <PKG>` build and deploy package to default target. (AWS)
* `sbpman --target local build <PKG>` build and deploy package to a target profile.
This option allows some packages to be kept in local repos.
* `sbpman sync <PKG>` build a pkgbuild from AUR; and deploy.
* `sbpman aurupdate` check AUR packages for updates, including upstream changes.
