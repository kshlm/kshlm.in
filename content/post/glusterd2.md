+++
title = "GlusterD2"
date = 2018-02-26T17:29:31+05:30
categories = ["gluster"]
tags = ["gluster", "glusterd", "glusterd2", "gd2", "gluster-4.0"]
+++

The Gluster-4.0 release is here, one of the most important releases for the Gluster community in quite some time.
The bump in the major version is being brought about by a few new changes, namely a change in the on-wire protocol,
 and the new management framework, GlusterD2 (GD2 for short).

GD2 has been under development for a very long time. We are excited to finally get it in the hands of users.
This post gives a sneak peek of GD2, what it brings in Gluster-4.0 and its future.

## What is GD2?

GD2 is the new management framework for Gluster-4.0 that has been written from scratch.
It has been written to be more scalable, maintainable and be easier to integrate with than before,
while still retaining the ease of use the original GlusterD (GD1) is known for.

The main points of interest with GD2 are as follows.

### Go

GD2 is written in [_Go_](https://golang.org).
_Go_ provides us with a language and tools that have been designed to help develop code that is more maintainable, and develop it faster.
It is a simple language to get started with, and is easy for C developers to get a hang of and become productive.
It also provides good interoperability with C, which is essential for GD2 to integrate well with the rest of GlusterFS which is written in C.

### ReST API

GD2 provides users with an HTTP and JSON based ReST API, in place of the earlier SunRPC/XDR management API.
This helps improve the integration story of Gluster a lot.

#### CLI

This ReST API is the default and only management interface supported by GD2.
A new CLI tool, `glustercli`, has been written targetting this API.
The new CLI retains much of the syntax and structure of the original `gluster` CLI, and will remain familiar to users.

### ETCD

One of the core changes in the way GD2 operates is that it now depends on [_etcd_](https://github.com/coreos/etcd) to store the cluster configuration data.
A big problem with GD1 that caused a lot of the scalability problems was the internal configuration store and its cluster wide synchronization.

GD2 avoids this by relying on _etcd_ as a centralized store of cluster configuration data.
_etcd_ takes care of ensuring that the cluster configuration data is safely replicated and highly available.

To keep it simple for Gluster users, GD2 contains an embedded instance of _etcd_ and automatically creates and manages an _etcd_ cluster.
If a user requires (say when running GD2 in Kubernetes), GD2 can also use an already running _etcd_ cluster.

### Flexible transaction framework

The transaction framework is used by GD2 to perform the cluster wide operations required to build and manage a Gluster pool.

GD2 contains a better internal transaction framework that has been built from the experiences gained from the multiple previous frameworks built for GD1,
and the knowledge that _etcd_ takes care of configuration storage.

The new transaction framework allows developers to write essentially free form transactions, instead of enforcing a particular form.
This allows each operation to operation to have a transaction tuned to its needs, and helps keep code simple and maintainable.

The framework now also includes roll-back capabilities, that can perform cluster wide roll backs of any changes done, when a transaction fails.

### Flexible volgen

GD2 implements a better structured and simpler way for developers to define volume graphs.
This reduces the effort needed to extend graphs or add new volume graphs.

### Better volume options

GD2 automatically loads the available volume options directly from the binary xlator file, and make them available for setting on volumes.
This prevents the need for xlator developers to modify GD2 to enable support for new options, and gives them complete control on what they want exposed to users.

### Plugins

GD2 defines a plugin interface that allows most Gluster management features to be implemented as plugins.
Most non-core volume management features (eg. geo-replication, snapshot) are being implemented using this interface.
External plugins are not supported right now, as the interface is not yet consumable for external plugins. This will change in the future.

## GD2 in Gluster-4.0

In Gluster-4.0, GD2 is being provided as a technical-preview, and is not recommended for production use.
We currently aim to reach full support for GD2 with Gluster-4.1, which is also planned to be the first long-term maintenance release of the Gluster-4.x series.

Due to this, Gluster-4.0 will ship with both the older GD1, and GD2.

GD2 in Gluster-4.0 has several limitations and known issues. These are described below.

### Available operations

In Gluster-4.0, GD2 implements most of the basic cluster and volume management operations, and a few others.
The available operations include,

- Cluster management
    - Peer probe
    - Peer detach
    - Peer status
- Volume management
    - Create/Start/Stop/Delete volumes
    - Expand
    - Set/Get options
- Bitrot
    - Enable/disable
    - Configure
    - Status
- Geo-replication
    - Create/Start/Pause/Resume/Stop/Delete sessions
    - Configure
    - Status

Many of the remaining features are being implemented, and will become available in Gluster-4.1.


### Backwards compatibility

GD2 is not backwards compatible with the older GD1. This means a hybrid cluster, running both GD1 and GD2, is not possible.

> Note: GD2 still retains backwards compatability with Gluster-3.x clients. Older clients will still be able to mount volumes created and managed using GD2.

### Upgrades

In Gluster-4.0, upgrades from Gluster-3.x to Gluster-4.0 using GD2, or migration from Gluster-4.0 using GD1 to GD2 are not supported.
Users running a Gluster-3.x cluster can upgrade to Gluster-4.0, with GD1 and continue using GD1.
To use GD2, users will need to start a fresh cluster.

This will be addressed with Gluster-4.1, with which GD2 will support upgrade/migration from GD1.
The upgrade/migration process will be designed to keep the filesystem available always, without downtime,
and very minimal downtime for the management layer.

### Problems with 2-node clusters

GD2 has problems when running with just 2 node clusters. Refer [#332](https://github.com/gluster/glusterd2/issues/332) and [#352](https://github.com/gluster/glusterd2/issues/352).
It is recommended to run a GD2 based Gluster cluster using either 1 node or, 3 or more nodes

## Future of GD2

GD2 is still under heavy development and is constantly recieving new features.
It is developed in a seperate repository, [gluster/glusterd2](https://github.com/gluster/glusterd2), seperate from the core GlusterFS repository.
[Github pull-requests](https://github.com/gluster/glusterd2/pulls) are used as the contribution mechanism, and [Github issues](https://github.com/gluster/glusterd2/issues) are used to track development.

Some of the expected future changes for GD2 include the following.

### Release cadence

GD2 will be released in lockstep with the Gluster release cycle, and will follow the same versioning.
This means a there will be a major GD2 release every 3 months and a maintenance release every month.

### Operations parity with GD1

GD2 currently has many operations supported by GD1 still under development. These include features like quota, snapshots, and full featured heal and rebalance operations.
It is planned to have all of these (and more) ready by the time of Gluster-4.1.

### Automatic volume provisioning

The [_Heketi_](https://github.com/heketi/heketi) project, provides a ReST API over GD1, and provides facility to automatically provision and create volumes for users.
Given an expected size and the features required of a volume, _heketi_ can provision the required bricks, select a suitable volume layout and create the volume.
In addition, _heketi_ provides other APIs relevant for Kubernetes.

The automatic provisioning abilities of _heketi_ will be moved over to GD2, and will be provided in the future directly from GD2.
This is currently under development, and expected to be available in Gluster-4.1.

### Workflow APIs

Along with the intial automatic volume provisioning APIs, we plan to add more APIs to GD2 to support volume and cluster maintenance (day-2) operations.
These will provide users with a sinlge API to perform some of the more complex and involved maintenance operations.
These APIs are expected to be available beyond Gluster-4.1.

### Monitoring and Tracing

We plan to instrument GD2 to provide better monitoring and tracing of the operations it performs, and provide the ReST APIs necessary to access this information.
Monitoring will be [_Prometheus_](https://prometheus.io) compatible, and tracing will be conformant to the [_OpenTracing_](http://opentracing.io) standard.
Some of this support is expected in Gluster-4.1, and more will become available in the future.

## Some closing comments

GD2 has been a result of the work many Gluster developers, without whom the current release wouldn't be possible.
Especially, the work of Prashanth([@prashanthpai](https://github.com/prashanthpai/)) and Aravinda([@aravindavk](https://github.com/aravindavk)), my GD2 co-maintainers.

The current history of GD2 development starts sometime in mid-2015.
I have been involved in GD2 since the beginning (and even before).
Most of the early development involved just me, and progressed very slowly.
Development kicked up a notch when Prashanth began contributing in mid-2016,
and once more when Aravinda joined early-2017.
Both of them have since taken up maintainership of GD2, and have been hugely responsible in helping new developers get started with GD2.
Without their work, GD2 wouldn't have reached the state it is at right now.

GD2 is a work in progress, and will continue to develop over the Gluster-4.x series. Our aim is to make GD2 the perfect management tool for Gluster.
Any help is always appreciated. Also, feedback is welcome. Please try out GD2 and let us know what your opinions.

PS: This post was also published on the Gluster community [blog](https://www.gluster.org/more-about-gluster-d2/).
