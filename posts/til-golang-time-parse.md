---
title: "TIL: Parsing dates and times in Go with time.Parse(...)"
date: 2021-09-10
tags:
  - til
  - golang

layout: layouts/post.njk
---

The Go module [time](https://pkg.go.dev/time) provides standard library functions for manipulating time and date, including the Parse method with this signature:

```go
func Parse(layout, value string) (Time, error)
```

`layout` is a string describing the format of the date or time value to be parsed. The `time` module provides several standard layouts as constant strings:

```go
const (
    ...
    RFC822      = "02 Jan 06 15:04 MST"
    ...
    Stamp      = "Jan _2 15:04:05"
    ...
)
```

Many additional layouts are provided, as well as instructions for creating your own layout string, at the [`time` module documentation](https://pkg.go.dev/time#pkg-constants).

This allows for straightforward parsing and manipulation of times and dates:

```go
time, err := time.Parse(time.Stamp, timestampString)

if err != nil {
    panic(err)
}

fmt.Println(time.Format(time.RFC822))
```

`value` is just the string to be parsed. It should contain only the actual time/date string. If the string contains any text that does not match the specified format, you will receive an "extra text" error:

```log
panic: parsing time "Sep  5 13:32:02 dnsmasq[8211]: query[AAAA] crawl.akrasiac.org from <INTERNAL IP>": extra text: " dnsmasq[8211]: query[AAAA] crawl.akrasiac.org from <INTERNAL IP>" 
```

The result of a properly formed call to `time.Parse(...)` with an appropriate `layout` and `value` is a Go time object. By default, unless your string includes time zone information, the time zone for the new time is UTC. `time.ParseInLocation(...)` provides the ability to define a time zone by providing a `Location` parameter, such as one returned by `time.FixedZone(...)` or `time.LoadLocation(...)`.

