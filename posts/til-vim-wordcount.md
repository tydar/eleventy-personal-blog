---
title: "TIL: Count words in a buffer or block in Vim"
description: Quick way to count words in a document in Vim
date: 2021-01-05
tags:
    - vim
    - til
layout: layouts/post.njk
---
To count the number of words in your current buffer in Vim, use the keystrokes `g` followed by `Ctrl-g`. You'll see something like:

``` text
Col 1 of 0; Line 13 of 14; Word 57 of 58, Byte 327 of 331
```

You can do the same with a selected block. Just select using the keystroke `V` combined with movement keystrokes or commands followed by `g` and `Ctrl-g`.
