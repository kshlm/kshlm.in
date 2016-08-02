+++
date = "2016-07-22T18:45:03+05:30"
title = "An update on this site"
tags=["site"]
categories=["site"]
alias=["an-update-on-this-site"]
+++

3 posts and nearly 2 years since I moved kshlm.in to [Ghost][1], I moving off it.

This site used to run on a free [Openshift Online][2] Node.js gear.
Even though I didn't post regularly, I'd kept Ghost updated (moved from a 0.5.x release to 0.7.x).
Updating was a manual effort, because I'd originally created the site using a template that I'd found on Github,
before a community supported cartridge was available in Openshift.

An attempt to update Ghost to the latest version (0.8.0) failed miserably.
This new version has so much more dependencies, that the free gear ran out of its 1GB storage quota.
Assuming that I ran out of space because of the cruft built up due to the many previous updates,
I tried to install Ghost on a empty gear. This install attempt also failed by running the gear out of space.

When I was looking to move of Tumblr to a self managed platform (I like having things I can control),
I chose Ghost because it was a light blogging platform compared to Wordpress, which would work well on the low resources of the free gear.
The updates have made Ghost bloated with large number of dependencies, and unsuitable for the free gear.
Trying to use something so heavy to host a very small site didn't seem good anymore.
So I made the decision to move away from Ghost.

I'd been playing around with [Scaleway][3] C1 bare-metal ARM servers for sometime, and decided to host the site on one such server.
I had also recently heard of [Caddy][4], a HTTP2 webserver written in Go, and decided I want to use it.
Caddy has really good integration with [Hugo][5], a static site generator also written in Go, which I chose as the static site generator.

To convert Ghost data to Hugo flavoured markdown files I used [ghostToHugo][6].
ghostToHugo requires a Ghost data export, which is JSON file, that can be generated and downloaded from the Ghost web admin interface.
I didn't have a Ghost server running (my two attempts had failed).
To generate the JSON export, I ran a local Ghost instance against the sqlite Ghost database that I'd SCPed off the now useless gear.

I committed the markdown files into a Git repository. I created a Hugo config, comitted it and pushed the repository to Github.
I wanted to keep resource usage to a minimum, so I created a new C1 ARM server with [Alpine Linux][7].
Alpine Linux couldn't run the ARM binaries of Caddy and Hugo I downloaded (something related to musl/glibc, IIRC).
I tried to compile Caddy and Hugo on the server, but this wasn't a very nice experience.
I gave on Alpine after sometime, and decided to move to Arch Linux, which I'm very familiar with.
But Scaleway had run out of ARM servers and I had already deleted the Alpine server.
To avoid a very long downtime, I created a VC1 X86 Virtual server.
As Arch Linux isn't available on VC1 instances, I chose the next most familiar option to me, CentOS 7.

I downloaded Hugo and Caddy. I pointed Hugo the cloned repo, and pointed Caddy to generated HTML files.
After a quick test to verify the site is working, I changed the DNS entries to point to new server.

This would seem to be a lot of effort to host a tiny static website, when I could just have used Github Pages + Jekyll.
But as mentioned earlier, I like having control over things and I also like to play around with a new things.
Because I did it this way, I get to play with things like HTTP2 and Let's Encrypt.
I'll keep playing with this till I get bored or I accidentally destroy this server.

For the curious, I've listed down most of the tools and services I'm using to run this site [here][8].

[1]: /moved-to-ghost/
[2]: https://www.openshift.com/
[3]: https://www.scaleway.com/
[4]: https://caddyserver.com/
[5]: https://gohugo.io/
[6]: https://github.com/jbarone/ghostToHugoA/
[7]: https://www.alpinelinux.org/
[8]: /about-this-site/
