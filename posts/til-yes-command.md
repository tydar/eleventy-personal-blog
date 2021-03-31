---
title: "TIL: Use yes to provide input to terminal programs"
date: 2021-01-27
tags:
  - til
  - linux

layout: layouts/post.njk
---
Recently my build pipeline started failing suddenly for KVM Conjurer. Quick investigation led to a an issue in `poetry`. The `TooManyRedirects` error can be addressed by running the command `poetry cache clear --all pypi`. So I added this to my `.gitlab-ci.yml` file and pushed the change. But the build still failed!

The `poetry cache clear` command requires input. To fix that issue, I learned about the coreutils program `yes`. If you just open a terminal and type it you'll get:

```bash
$ yes
y
y
y
y
y
y
y
y
y
y
. . .
```

`yes` outputs the given value (by default 'y') until it is killed. So I changed my `.gitlab-ci.yml` line to be:

```yaml
test-job:
    . . .
    before_script:
        - yes 'yes' | poetry cache clear --all pypi
    . . .
```
