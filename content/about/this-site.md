+++
title = "About this site"
tags = ["tools", "services"]
slug = "this-site"
date = "2019-01-22T23:30:02+05:30"
+++

This page contains the list of the awesome free tools and (mostly) free services being used to serve this site.
Some of these might be a little too much for a small site like this, but I just like to try out lots of things.

## Tools

### Caddy

[Caddy](https://caddyserver.com/) is a really easy to use webserver used to serve this site.
It serves HTTP2 by default and has [automatic HTTPS](https://caddyserver.com/docs/automatic-https).

Caddy has been configured to serve the site over HTTP2 using [TLS certificates]({{<relref "#acmetool">}}) from [Let's Encrypt]({{<relref "#let-s-encrypt">}}).
The git (with a Github hook) Caddy plugin, are used to automatically deploy updates to the site.

Caddy has been installed using the [getcaddy.com](https://getcaddy.com) script, with the http.git, http.minify and a few other plugins enabled.

### CentOS

The site is served off a [CentOS](https://www.centos.org/) 7.4 VM hosted on [Scaleway]({{<relref "#scaleway">}}).

### Git and Git-LFS

The contents of the site are version controlled using [Git](https://git-scm.com/), with [git-lfs](https://git-lfs.github.com/) used for binary files.

Git-LFS has been installed using the RPM packages provided by [packagecloud](https://packagecloud.io/github/git-lfs/install).

### Hugo

[Hugo](https://gohugo.io/) is the really awesome static site generator used to generate this site from markdown files stored in Git.

Hugo has been installed using the [daftaupe/hugo](https://copr.fedorainfracloud.org/coprs/daftaupe/hugo/) copr repository.

The site uses a modified version of the [Indigo](https://github.com/AngeloStavrow/indigo) theme.


## Services

### Cloudflare

[Cloudflare](https://www.cloudflare.com/) is used as the DNS provider for the site. Cloudflare's automatic CDN, caching and security services are also being used. And all of this is FREE!

Because Cloudflare's CDN and security services are being used, for most (all) people, the site will be served from one of Cloudflare's servers, with a Cloudflare SNI certificate.
Cloudflare's 'Full SSL (Strict)' option has been enabled to ensure that Cloudflare is caching resources served only from the Scaleway VM.

The site can be viewed without Cloudflare's intervention by visiting [blog.kshlm.in](https://blog.kshlm.in/) for verification.

### Github

[Github](https://github.com/) provides git and git-lfs hosting. The source for the site (and the Hugo configuration) are available at [kshlm/kshlm.in](https://github.com/kshlm/kshlm.in).
Github has been configured to notify Caddy via a webhook on updates to the repository.

### Let's Encrypt

[Let's Encrypt](https://letsencrypt.org/) is used to obtain the TLS certificates for kshlm.in and blog.kshlm.in.
Let's Encrypt provides free and automated TLS certificates using the [ACME protocol](https://github.com/letsencrypt/acme-spec).

When visiting kshlm.in, content might be served from Cloudflare's CDN servers with Cloudflare SNI certificate (see note in [Cloudflare]({{#relref "cloudflare"}})).
The Let's Encrypt certificate is used to ensure that Cloudflare's Full SSL (Strict) option works correctly.

### Scaleway

[Scaleway](https://www.scaleway.com/) is a cloud provider offering bare-metal (and virtual), SSD backed, ARM and X86 servers.
The site is currently hosted on a VC1 virtual X86 server.
