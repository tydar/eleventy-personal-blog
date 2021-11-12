---
title: "TIL: Go reflect.DeepEqual to compare maps"
date: 2021-11-11
tags:
    - til
    - golang
layout: layouts/post.njk
---
When trying to compare a map, slice, or struct in Go it may be necessary to use [reflect.DeepEqual](https://pkg.go.dev/reflect#DeepEqual) instead of the standard equality operator `==`.

The rules for `reflect.DeepEqual` are defined in the above link. They don't always work how you may expect -- in those cases, it may be necessary to roll-your-own comparison function.
