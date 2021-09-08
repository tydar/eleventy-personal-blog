---
title: "TIL: Use strings.Fields() to split strings on whitespace in Go"
date: 2021-09-08
tags:
  - til
  - golang

layout: layouts/post.njk
---
I've been working on an introductory Go project that involves processing logs from my Pihole. The log has lines that look like this:

```log
Sep  8 00:01:27 dnsmasq[8211]: cached github.com is 140.82.112.3
```

To parse these lines, I need to split them on whitespace. Initially I used this method invocation:

```go
tokens := strings.Split(line, " ")
```

This gave me problems. The split would return a slice with elements of the log line and different indicies depending on the timestamp. If the entry occured on a single-digit day of the month, there would be an extra element in the slice compared to double-digit days of the month.

In Go, to split a string with each token separated by consecutive whitespace characters, instead use this function from the `strings` module:

```go
tokens := strings.Fields(line)
```
