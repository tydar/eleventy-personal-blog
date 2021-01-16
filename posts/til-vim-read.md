---
title: "TIL: pipe output from terminal command into Vim"
date: 2021-01-08
tags:
    - til
    - vim
layout: layouts/post.njk
---
To execute a shell command from Vim and send the input to your current buffer, you can use the `read` command. To create the directory tree in my [post about Ansible roles](/posts/convert-playbook-to-role), I used this Vim command:

``` vim
:read !tree --charset=ascii
```

from the root directory of my roles project.
