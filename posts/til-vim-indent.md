---
title: "TIL: VIM and indentation"
date: 2021-01-14
tags:
    - vim
    - til
layout: layouts/post.njk
---
For a while, I'd often used this set of Vim keystrokes: `gg=G`. These keystrokes autoindent for the whole file.

Editing a lot of Ansible .yml with VIM has led to me needing to indent and unindent individual lines much more frequently.

To indent just the current line one step use `>>`. To unindent the current line one step use `<<`.

These commands also work with the usual Vim movement and visual selection commands. So `Vjjj>` will indent four lines one step. The keystrokes `4>>` similarly indents 4 lines.

You can also use `==` to indent just the current line according to your autoindent settings. The `=` command can also be combined with visual block selection or motions.
