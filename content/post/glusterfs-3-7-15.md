+++
date = "2016-09-01T11:50:24+05:30"
title = "GlusterFS 3.7.15"
categories = ["gluster"]
tags = ["gluster", "glusterfs", "release"]
slug = "glusterfs-3-7-15"
aliases = ["glusterfs-3-7-15"]
+++

# GlusterFS 3.7.15 released

GlusterFS 3.7.15 has been released. This is a regular scheduled release for GlusterFS-3.7 and includes 38 bug fixes since 3.7.14.
The release-notes can be read [here][1].

## Downloads

The tarball can be downloaded from [download.gluster.org][2].

### Packages

Binary packages have been built are in the process of being made available as updates.

The CentOS [Storage SIG][3] packages have been built and will become available in the centos-gluster37-test repository (from the centos-release-gluster37 package) shortly.
These will be made available in the release repository after some more testing.

Packages for Fedora 23 are queued for testing in Fedora Koji/Bodhi. They will appear first via dnf in the Updates-Testing repo, then in the Updates repo.

Packages for Fedora 24, 25, 26; Debian wheezy, jessie, and stretch, are available now on [download.gluster.org][2].

Packages for Ubuntu Trusty, Wily, and Xenial are available now in Launchpad.

Packages for SuSE available now in the SuSE build system.

See the READMEs in the respective subdirs at [download.gluster.org][2] for more details on how to obtain them.

## Next release

GlusterFS-3.7.16 will be the next release for GlusterFS-3.7, and is currently targetted for release on 30th September 2016.
The [tracker bug][4] for GlusterFS-3.7.16 has been created. Bugs that need to be included in 3.7.16 need to be marked as dependencies of this bug.



[1]: https://github.com/gluster/glusterfs/blob/release-3.7/doc/release-notes/3.7.15.md
[2]: https://download.gluster.org/pub/gluster/glusterfs/3.7/3.7.15/
[3]: https://wiki.centos.org/SpecialInterestGroup/Storage
[4]: https://bugzilla.redhat.com/show_bug.cgi?id=glusterfs-3.7.16
