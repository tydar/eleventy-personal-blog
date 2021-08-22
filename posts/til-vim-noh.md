---
title: "TIL: clear search highlighting in Vim"
description: Search highlighting in Vim is persistant. Use :noh to clear it.
date: 2021-08-22
tags:
   - vim
   - til

layout: layouts/post.njk
---

To remove the search highlighting from a Vim buffer, use this command: `:noh`. That's short for `:nohlsearch`. [^0]

Today I was copying a list of variables from a markdown file. Since they were in a markdown list, each line began with an asterisk:

```markdown
* git_clone_location: local directory for pihole checkout
    .
    .
    .
* pihole_password: password for the pihole web interface
```

Since I was moving these variables to YAML file for use with Ansible, I needed to strip the asterisk and space from the beginning of each line. A quick google brought me this solution `%s/^../`. That command matched the first two characters of each line and substituted in an empty string (so, it deleted them). 

Once I ran the command, Vim's search highlighting remained at the beginning of each line. I've seen this a thousand times but never though to look for a solution--usually after searching I'd move on with my work and ignore it or be done with the buffer anyways.

Today, I still had editing to do, so I finally looked it up and learned this command!

[0](https://stackoverflow.com/questions/4372660/get-rid-of-vims-highlight-after-searching-text)
