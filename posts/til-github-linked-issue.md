---
title: "TIL: Automatically link PR to issue on GitHub"
date: 2021-12-16
tags:
  - til
  - github

layout: layouts/post.njk
---
To link a PR to an issue on GitHub automatically (so that it closes the issue when the PR is merged), you can use certain language like 'fixes #10' or 'closes #10' as detailed on [this docs page](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue). You can put this language in either the description of a PR or in a commit message.

If you use the language in a PR, merging that PR will cause the issue to close. If the language is used in a commit message instead, the PR is not treated as "linked" but the issue will close when the commit itself is merged.
