---
title: "TIL: Using anonymous functions in Go"
date: 2021-09-11
tags:
  - til
  - golang

layout: layouts/post.njk
---
In my current Go project, I often need to filter a slice of `struct`s based on the value of some field. Writing a unique filter function or simply rewriting the logic in place was tedious. Using anonymous functions, a more generic filter function can be created.

My `struct` type is:

```go
type logLine struct {
	Timestamp time.Time 
	LineType  string    
	Result    string    
	Domain    string    
	Requester string    
	Upstream  string    
	Line      string    
}
```

My generic filter function looks like this:

```go
type filterFunc func(logLine) bool

func filterLogLine(lines []logLine, f filterFunc) []logLine {
	// filterLogLine filters a slice of logLines using f to determine inclusion
	// f is type func(logLine) bool
	var filtered []logLine
	for i := range lines {
		if f(lines[i]) {
			filtered = append(filtered, lines[i])
		}
	}
	return filtered
}
```

So I can write function invocations like this to clearly and concisely filter my parsed log file:

```go
filtered := filterLogLine(logLines, func(ll logLine) bool {
  return ll.Result == selectedLine.Result
})
```

Using an anonymous function also enabled writing this helper for searching the full text of a log line:

```go
func textSearchLogLine(line logLine, s string) filterFunc {
	// textSearchLogLine is a helper function to generate a filterFunc
	// that searches for text s anywhere in a logLine
	return func(ll logLine) bool {
		return strings.Contains(ll.Line, s)
	}
}
```

Generally the syntax for an anonymous function in Go is:

```go
func (argument argType) returnType {
  // function body
}
```