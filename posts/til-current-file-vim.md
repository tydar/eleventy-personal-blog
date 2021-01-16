---
title: "TIL: display path to current file in Vim"
date: 2021-01-16
tags:
    - vim
    - til
layout: layouts/post.njk
---
To output the path to the file you're currently editing in Vim, press `Ctrl-G` in normal mode. In the status bar at the bottom of the window Vim will output the path to the current file and other information about the current buffer.

This is a normal mode shortcut to the ex-command `:f`, itself short for `:file`.  To print just the current path in the status line, use `:echo expand('%')`. You can print the location of the current file into your buffer using a set of commands like `:redir @a | echo expand('%') | redir END | put a`.

A bit wordy, but this command temporarily `redir`ects output to the register `a`, `echo`es the output of `expand('%')` into that register, ends our `redir`ect, and the `put`s the content of register `a`.
