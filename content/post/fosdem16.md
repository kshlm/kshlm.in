+++
author = "Kaushal"
categories = ["gluster", "fosdem", "fosdem'16"]
date = 2016-02-16T12:53:48Z
draft = false
image = "/images/2016/02/flyerfosdem.png"
slug = "fosdem16"
tags = ["gluster", "fosdem", "fosdem'16"]
title = "Gluster (and me) @ FOSDEM'16"
aliases = ["fosdem16"]

+++

My first [FOSDEM](https://fosdem.org/2016/), and it was awesome. I got to meet a lot of GlusterFS users and understood a lot about how they use GlusterFS. And I also got to meet and hear Gophers at the Go Devroom. Thank you Red Hat for sponsoring my trip here.

## Gluster @ FOSDEM'16
I was part of the Gluster team at FOSDEM, and hosted a Gluster stand for 2 days along with Humble ([@hchiramm](https://twitter.com/hchiramm)) and Amye ([@amye](https://twitter.com/amye)). We also had Neils ([@nixpanic](https://twitter.com/nixpanic)) drop in and help out with the stand as well.

This was the first time a Gluster stand was put up in a conference as large as FOSDEM, and we didn't come prepared fully. We had goodies (stickers, pens, lanyards) to give away. But as we didn't have any brochures, posters or other sorts of information displays, we didn't get much traction initially. We did have existing users of Gluster stop by and talk. Much of the responses I got were happy.

In the afternoon, I created a single page slide with an overview of GlusterFS [^1], put it full-screen on my laptop and turned it towards the visitors. But I'd forgotten to disable the screen auto-off power saving setting, and so I sat for an hour wondering why no one was looking at the screen. After I'd realized this and fixed it, the stand got more interest. New visitors drawn in by the overview graphic, who hadn't used GlusterFS before, promised to give GlusterFS as spin.

At nearly the same time, Neils was giving a talk on the [Gluster roadmap](https://fosdem.org/2016/schedule/event/gluster_roadmap/). We had more interest after this, and got a lot of queries on our new features, in particular arbiter and sharding. We had somewhat of a steady stream of visitors to the stand, till the end of the day. The first day closed at around 6.30PM.

The second day started well. We had Neils available at the stand for a longer time, as I spent some time at the Go devroom (more on this later). Neils' presentation [^2] from the previous day was put on loop on Humble's laptop, and we were set. Kaleb([@kalebkuechle](https://twitter.com/kalebkuechle)) dropped in as well. The crowd was less on the second day, and the day wound down early at around 5PM. The teams manning the [oVirt](https://www.ovirt.org) and [Foreman](http://theforeman.org) stand located beside the Gluster stand, we very helpful in getting the location cleaned up.

### My notes, observations
The list below isn't ordered in any particular way, so please excuse me.

- Kids find the Gluster stickers with the ant mascot (does it have a name?) very attractive. We had lots of kids pick up the stickers and some parents did mentioned that the kids liked them a lot. A couple of brothers, came around several times and picked up stickers (some times sneakily).
- Most users have small deployments of GlusterFS (2-4 servers), and it basically just works for them. They're really happy with the ease of deployment and use. This is something we must not ever lose IMO.
- Even though we recommend not using GlusterFS for small files many of the user use it for small file situations. Several people using it for hosting websites, but one of the users is using it for git repositories. And they said it works well enough for them. One user said they'd disabled replication, because it was slower and were using pure distribute.
- Lot's of people were interested in integration with containers (think Docker and Kubernetes). I know that some things are happening around this area, but we should better publicize this.
- Lot's of interest in virtualization as well. Lots of questions on arbiter and it's support in oVirt after Neils' talk. Many of the users I spoke to about this topic didn't know of these before the talk.  This shows that we need to spend more effort publicizing these new features.
- Surprisingly, lots of people are on the latest release (3.7.6 at the time of FOSDEM). The oldest deployment I heard of was on 3.3, which wasn't updated because the management wasn't interested upgrading something that is working well.
- Not a lot of complaints. I heard of just one complaint on GlusterFS processes getting hung up when used with small files. But couldn't get more information.
- The OpenMandriva community will be using GlusterFS for storage on their upcoming build farm. We should be on the lookout for an announcement on this soon. A nice gentleman from the OpenMandriva community stopped by to let us know of this, and commended on our ease of use.
- We had a user who was using GlusterFS on Power processors, without problems. The clients and servers were both using Power.
- A user who came to know of libgfapi, was wondering if it could be used for applications on Windows. He spoke particularly of QEMU, but I think this could be some thing that we could investigate.

### Takeaways from FOSDEM
- GlusterFS should never lose its ease of deployment and use.
- Not a lot of the community users are looking at very large deployments. We should keep this in mind.
- We should do a better job publicizing new features.
- We should prepare much better if we are to host stands at conferences. We need to provide more visual information to visitors. We need more brochures, posters and displays. But Amye was there with us and is aware of this and is already on top of this.
- We should also prepare some sort of a live demo. Visitors passing by the oVirt/Foreman stand besides us were pulled in by their demos.
- We should try to get more core developers out to the stands. We had a lot of users come by with doubts on features, that I felt I wasn't able to answer satisfactorily. Having someone who can answer these will be good, and will leave the visitors happier.
- (This one's for me) I need to initiate more conversations, and not just nod at people when they look at me.

## Me @ FOSDEM'16
Part of the reason I got to go to FOSDEM, was that I was invited to speak at the Go devroom. My talk was titled ['Go & Plugins'](https://fosdem.org/2016/schedule/event/plugins/) [^3]. The talk was about what my experiences and findings from my investigations into implementing plugins for GlusterD-2.0 which is written in Go. When I'd submitted the application, I had hoped to have some sort of a implementation of plugins in GD2 to show as an example and walk-through in the talk. But this didn't happen, so my talk was pretty short and devoid of code samples which people had (probably) expected. But still, I'd like to think that the talk was a success, the room was nearly full and I got several genuine questions and suggestions at the end.


After FOSDEM, I attended DevConf 2016 as well. I'll be writing up about that soon (later?) in another post.



[^1]: Well I actually borrowed the overview image from the wonderful image that Rajesh and Poornima created for their GlusterFS workshop in LISA'15. The presentation can be found [here](https://gluster.readthedocs.org/en/latest/presentations/).
[^2]: Can be found [here](https://gluster.readthedocs.org/en/latest/presentations/) again.
[^3]: <del>Unfortunately the video isn't up yet. You can view the presentation linked in the page.</del> _Update Jul 22, 2016: The video is finally up. Pretty poor quality though._ I'll be writing up a blog post about the topic soon as well.
