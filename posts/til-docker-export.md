---
title: "TIL: save the state of a Docker container with docker export"
description: Instead of building a new image or tinkering under the hood with the filesystem, use docker export to tarball your running container.
date: 2021-01-01
tags:
  - docker
  - devops
  - til
layout: layouts/post.njk
---

For my homelab and software projects, I currently use an instance of DokuWiki to keep notes about progress, learnings, and future ideas someplace other than in my head. [DokuWiki](https://www.dokuwiki.org) is open-source wiki software that runs without a database. I primarily chose it for that feature -- it means it's very easy to set up in a Docker container and has low technical overhead. Less can go wrong when you're just writing and reading files.

On the other hand, because I'm running my DokuWiki instance in a Docker container, [the DokuWiki backup process](https://www.dokuwiki.org/faq:backup) -- just copy all of the files -- is less accessible. You *can* find standard Docker volumes on the filesystem, but tinkering there is discouraged.

I learned the canonical way to back up a running container is to simply run `docker export`. This command produces a tarball that can later be consumed by `docker import` to bring up a new container in the same state as the moment it was exported. This let me much more easily toss an easy-to-restore archive into an s3 bucket for safekeeping.

I'm not fully satisfied because the archive has the bloat of the full container state. I'd prefer just to back up the text of the wiki. I won't use this solution for backups forever. But it was a quick and easy way to make sure I didn't lose my notes when I needed to power down the host running my wiki.
