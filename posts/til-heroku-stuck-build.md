---
title: "TIL: Stopping a Heroku build"
date: 2022-01-14
tags:
  - til
  - heroku
  - devops
layout: layouts/post.njk
---

Sometimes a build can become stuck or extremely sluggish on Heroku. To cancel a build, use this command taken from the [Heroku docs](https://help.heroku.com/4RNNGYNU/how-to-stop-a-stuck-build):

```shell-session
$ heroku plugins:install heroku-builds # only necessary if not already installed
$ heroku builds:cancel BUILD_UUID -a APP_NAME
```

The `BUILD_UUID` can be found in the top-right corner of the Build Log UI at `https://dashboard.heroku.com/apps/APP_NAME/activity/builds`.
