---
title: "TIL: Using a struct as a map key in Go"
date: 2021-09-18
tags:
  - til
  - golang

layout: layouts/post.njk
---
In Go, any comparable type can be used as the key in a map. This includes struct types, as long as all of the fields of the struct are comparable.

In a toy finite state machine implementation in Go, I used this feature to implement a check for a transition from a given state with a given input.

The `Event` type is:

```go
type Event struct {
	Input string
	From  string
}
```

`string` is a comparable type, so we can use `Event` as a map key like in the `FSM` type:

```go
type FSM struct {
	//...
	Events map[Event]string
	//...
}
```

And then check if an appropriate `Event` exists from a given state with a given input by using Go's map assignment syntax like so:

```go
func (f *FSM) GetEvent(start string, input string) (Event, error) {
	// GetEvent returns a single event and an error value based on a starting state and an input symbol
	e := Event{
		From:  start,
		Input: input,
	}
	_, present := f.Events[e]

	if present {
		return e, nil
	}

	return Event{}, fmt.Errorf("no event from state %s with input %s found", start, input)
}
```