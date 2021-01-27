---
title: "TIL: Use AutoHotkey hotstrings to ease recordkeeping"
date: 2021-01-27
tags:
  - til
  - helpdesk

layout: layouts/post.njk
---

To use AutoHotkey to ease recordkeeping on tickets, you can use the basic "hotstring" feature for frequently repeated issues. An example script:

```ahk
::/cs::Customer says
::/fpw::Reset password issue
::/rr::Reset wireless AP/router resolved cusomter issue
```

Once this script is running, AHK will replace any of the strings on the left side of the second `::` with the string on the right side of the `::`. So if you type `/fpw` into a text field in your ticketing system and then hit space, you'll see `Reset password issue` instead.
