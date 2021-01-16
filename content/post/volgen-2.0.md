+++
date = "2016-10-27T14:08:13+05:30"
title = "Volgen-2.0"
categories = ["dev", "glusterfs", "gluster"]
tags = ["development", "glusterfs", "gluster", "glusterd-2.0", "gd2", "volgen", "glusterfs-4.0"]
showtoc = true
tocopen = true
+++

# Designing and prototyping volgen for GD2

I've recently begun working on volgen for GD2. This post gives an overview of what I've been doing till now.

## Some background first

One of the requirements for GD2 is to make it easier to modify the volume graphs, in particular make it easier to add new translators into the volume graph.

GlusterD right now has hardcoded graph topologies in its code. This makes it very hard to begin modify graphs. This made it hard for features like tiering to do their stuff.

For GD2, we are designing a more flexible volgen package, that makes it much easier to modify, extend and combine graphs. We've begun working on a prototype to explore how this design would work in real life.

Different approaches to volgen was discussed earlier this year in a meeting during DevConf. The approaches discussed are summarized in the ["Flexible volgen" wiki][1].  Of the discussed approaches, the "SystemD units" style approach was picked as the most suitable.

I've begun a prototype implementation of this approach at [kshlm/glusterd2-volgen][2]. I welcome comments and queries on the approach as well as the prototype.

## The SystemD style volgen

> NOTE: Most of the names, paths etc. used below are just placeholders. They will all be changed to more decsriptive terms before we reach the end.

More information on this can be found on the wiki, but the wiki might now be always up to date. So I'm providing a quick summary of the approach here.

This approach to volgen makes use of what I'm currently calling Nodefiles. These are analogus to the systemd unit files. A Nodefile describes a node which can appear in a volume graph.
There are two types of nodes/nodefiles, Xlators and Targets

### Xlators

Xlators nodefiles describe GlusterFS translators. Nodefiles are text files, which have to follow a defined format, that describe the particular translator. Examples of Nodefiles can be found at [kshlm/glusterd2-volgen/examples/xlator][3]. _Note: These examples are just that; examples. They are not the final form._

In addition to describing a translator, Nodefiles specify the dependencies of the translator on other translator. Some of the dependecies currently available are,
- Requires, this translator requires the specified translator also to be enabled
- Conflicts, this translator cannot work with the specified translator
- Before, this translator must appear before the other translator in the volume graph
- After, this translator must appear after the specified translator in the volume graph
- Parent, this translator can only have the specified translator as its parent
- Child, this translator can only have the specified translator as its child(ren).

In the prototype, [TOML][4] is used as the description language. The Nodefile is described in code as a the `Node` struct in [volgen/nodefile.go][5]. _Note: This is incomplete right now._

Each translator will provide its own nodefile, all of which will need to be added to a directory known to GD2 (let's call it `XLATORDIR` for now), for eg. `/usr/lib/glusterd/xlators`.
To make it easier for authors to create these files, we may provide tools to scrape information from the C code and build the file. But that's a task for later.

GD2 will read the Xlator nodefiles from `XLATORDIR` at startup and build an internal translator map, and a transaltor options table.

### Targets

Targets are nodes which describe graphs. You can have Targets for FUSE graph, brick graph, self-heal daemon etc.

On disc, Targets are directories which have Xlators linked into it. The linked in translators are the translators which will be present in the graph. The Targets decribe themselves with an `info` nodefile in the directory. Specific dependencies for the target can also be given in the nodefile. An example target for the brick graph can be found at [kshlm/glusterd2-volgen/examples/brick.target][6]. A `Target` struct is described in [volgen/target.go][7].

Targets can include other targets among themselves. This will make it easier to describe complex graphs. For eg. a `fuse.target` for the FUSE graph can include a `performance.target `and `clients.target` among other translators. The `performance.target` includes all known performance translators, and would provide an easy way to enable/disable performance translators. The `clients.target` would just include the cluster translators, ie the part of the client graph below and including DHT. This will make it easier to generate other graphs, like NFS for example, which would now need to only need to include the `clients.target` instead of the whole client graph.

Targets are the starting points of building an actual volume graph. Once a target is loaded into GD2, building a graph will be as simple as calling `Target.BuildGraph(volinfo)`.

The `BuildGraph` function will first do a dependency resolution of all the translators/targets included in the target and build a intermediate graph. `BuildGraph` is called on any targets in this graph, and the returned graph is inserted at the targets location. Build graph will next fill in this intermediate graph with information from the volinfo, disable xlators if required and generate the actual volume graph. Calling `graph.Write()` will write out a volfile describing this graph.

The protoype contains a [simple app][8] which shows how to make use of this volgen packaged.

### Things left to be done

- I still need to design and implement the actual dependency resolver. I expect to take a significant amount of time for this. I'll keep everyone updated on my progress on this.
- I still have only a vague picture of how to handle assigning identities and options for client xlators, ie. how to get volgen to assign a client xlator to a brick.
- There is a question of how a graph with complex interconnections, such as the one for server side replication, can be described with this. This is something I expect to have an answer as I proceed further with the prototype.


[1]: https://github.com/gluster/glusterd2/wiki/Flexible-volgen
[2]: https://github.com/kshlm/glusterd2-volgen
[3]: https://github.com/kshlm/glusterd2-volgen/tree/volgen-systemd-style/examples/xlators
[4]: https://github.com/toml-lang/toml/blob/master/versions/en/toml-v0.4.0.md
[5]: https://github.com/kshlm/glusterd2-volgen/blob/volgen-systemd-style/volgen/nodefile.go
[6]: https://github.com/kshlm/glusterd2-volgen/tree/volgen-systemd-style/examples/brick.target
[7]: https://github.com/kshlm/glusterd2-volgen/blob/volgen-systemd-style/volgen/target.go
[8]: https://github.com/kshlm/glusterd2-volgen/blob/volgen-systemd-style/main.go


