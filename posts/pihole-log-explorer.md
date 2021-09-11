---
title: "Creating a TUI for Pihole log exploration in Go"
date: 2021-09-11
tags:
  - til
  - golang
  - pihole

layout: layouts/post.njk
---
As a first Go project I am writing a [TUI explorer for Pihole log files](https://github.com/tydar/pihole-log-explorer). This app makes use of the very easy to learn [tview module](https://github.com/rivo/tview) to build the UI for a simple log parser.

The app currently allows a user to browse the log file, search for arbitrary strings, and filter the log file by a number of properties like query types, requesters, and query results.

The TUI is built from a few `tview` primitives. One of the most convenient for me was the flexbox implementation:

```go
flex := tview.NewFlex().
		SetDirection(tview.FlexRow).
		AddItem(filterField, 3, 1, false).
		AddItem(filterIndicator, 3, 1, false).
		AddItem(tview.NewFlex().SetDirection(tview.FlexColumn).
			AddItem(detailPane, 0, 1, false).
			AddItem(table, 0, 2, true), 0, 1, true,
		)
```

This `tview` feature makes it so easy to layout the TUI without thinking too hard about pixels, columns, and rows.

Log lines are parsed into a struct:

```go
type logLine struct {
	Timestamp time.Time // Timestamp for line
	LineType  string    // Type of line. Interpreted by UI to determine actions
	Result    string    // Present for cached, reply, blocked
	Domain    string    // Present for cached, reply, blocked, query[*], forwarded
	Requester string    // Present for query[*]
	Upstream  string    // Present for forwarded
	Line      string    // Store full line text for UI purposes
}
```

The log is filtered based on the elements of the struct. For example, filtering by the type of query:

```go
// when an applicable detailPane list item is selected, filter the main table
detailPane.AddItem("Entry type: "+selectedLine.LineType, "", 0, func() {
	table.Clear()

	// LineType may have a tview-escaped closing square bracket, so we have to undo that
	filterIndicator.SetText(fmt.Sprintf("LineType: %v",
		strings.ReplaceAll(selectedLine.LineType, "[]", "]")))

	filtered := filterLogLine(logLines, func(ll logLine) bool {
		return ll.LineType == selectedLine.LineType
	})

	setTable(table, filtered)
})
```

It has been a while since I've used a strongly- and statically-typed language to do anything substantial. Having done most of my recent programming in Python and JS, I really appreciated the change. I didn't feel like the type system slowed me down at all and I found myself spending less time chasing down errors since many mistakes that are easy to make in Python and JS are caught by the compiler instead of silently producing no or managled output or requiring a backtracking the source of a value to determine the error.

Overall, I really, really like writing code in Go. I'm excited to finish this project and move on to writing an http project in Go! 