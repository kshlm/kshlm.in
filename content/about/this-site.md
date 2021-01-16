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

The site uses a modified version of the [Indigo](https://github.com/AngeloStavrow/indigo) theme.

## Services

### Cloudflare

[Cloudflare](https://www.cloudflare.com/) is used as the DNS provider for the site.

### Forestry

[Forestry](https://forestry.io "Forestry") is a Git backed CMS that works with multiple static site generators. This site uses Forestry to manage the content in [kshlm/kshlm.in](https://github.com/kshlm/kshlm.in).

### Github

[Github](https://github.com/) provides git hosting. The source for the site (and the Hugo configuration) are available at [kshlm/kshlm.in](https://github.com/kshlm/kshlm.in).

#### Github Pages

This static site is hosted on [Github Pages](https://pages.github.com "Github Pages") and served from a custom domain with HTTPS enforced.

#### Github Actions

A [Github Actions](https://github.com/features/actions "Github Actions") [workflow](https://github.com/kshlm/kshlm.in/blob/master/.github/workflows/gh-pages.yml) is used to build and deploy this site into Github Pages. It uses [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo) and [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages) actions to perform the build and deploy.
