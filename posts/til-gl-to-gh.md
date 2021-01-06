---
title: "TIL: set up push mirroring from GitLab to GitHub"
description: GitLab repositories offer free push mirroring to GitHub
date: 2021-01-06
tags:
   - gitlab
   - til

layout: layouts/post.njk
---
The GitLab free tier only allows free mirroring *to* GitHub, not pulls from GitHub. That requires the first paid tier. To set up your push mirroring follow these steps:

1. Go to your project on GitLab. Click *Settings -> Repository*.
2. Click to expand the *Mirroring repositories* section.
3. Enter your Git repository URL. If you're on the free tier like I am, the *Mirror direction* option will be locked to *push*. Otherwise, pick *push* or *pull* as you'd like.
4. Choose an authentication method.
5. Click *Mirror repository* to set up the mirroring.

You should see in the *Mirroring repositories* section an entry for your GitHub repo such as `https://****:*****@github.com/tydar/kvm-conjurer.git`. You'll see also *Last update attempt* and *Last successful update* columns, indicating when the push to GitHub from GitLab was last attempted and whether it succeeded. On a push mirror setup, GitLab should push your changes to GitHub as they come in.
