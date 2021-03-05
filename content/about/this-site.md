+++
tags = ["tools", "services"]
title = "About this site"

+++
This page contains the list of the awesome free tools and services being used to serve this site.

## Tools

### Git and Git-LFS

The contents of the site are version controlled using [Git](https://git-scm.com/), with [git-lfs](https://git-lfs.github.com/) used for binary files.

### Hugo

[Hugo](https://gohugo.io/) is the really awesome static site generator used to generate this site from markdown files stored in Git.

### IPFS

This site is deployed to [IPFS](https://ipfs.io/) and available at [ipns://kshlm.in/](ipns://kshlm.in/).

## Services

### Cloudflare

[Cloudflare](https://www.cloudflare.com/) is used as the DNS provider for the site. [Cloudflare IPFS gateway](https://cloudflare-ipfs.com) is used to serve the IPFS site to the general web.

### Forestry

[Forestry](https://forestry.io "Forestry") is a Git backed CMS that works with multiple static site generators. This site uses Forestry to manage the content in [kshlm/kshlm.in](https://github.com/kshlm/kshlm.in).

### Github

[Github](https://github.com/) provides git hosting. The source for the site (and the Hugo configuration) are available at [kshlm/kshlm.in](https://github.com/kshlm/kshlm.in).

#### Github Actions

A [Github Actions](https://github.com/features/actions "Github Actions") [workflow](https://github.com/kshlm/kshlm.in/blob/master/.github/workflows/gh-pages.yml) is used to build and deploy this site into Github Pages. It uses the [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo) action to build and a custom deploy-ipfs job using [ipfs-deploy](https://github.com/ipfs-shipyard/ipfs-deploy) do deploy the site to IPFS.

### Infura / Pinata

IPFS pinning services provided [Infura](https://infura.io/) and [Pinata](https://pinata.cloud/) are used to make this site available on IPFS.
