---
title: "Simple regex written as FSM"
date: 2021-09-19
tags:
  - golang
  - regex
  - FSM

layout: layouts/post.njk
---
Today and yesterday I have been toying with a [simple finite state machine](https://github.com/tydar/gone-fsm) implementation I wrote in Go.

One common use of finite state machines is for the computation of regular expressions. A parser is a bit out of scope for messing with my simple library (though I am interested in trying that out later).

Instead I manually wrote out a couple of simple examples. One easy FSM is equivalent to the regex `^a+$`:

```go
func oneOrMore(s string) (*fsm.FSM, error) {
	// oneOrMore returns an FSM that matches a regex like ^a+$
	if len(s) != 1 {
		return &fsm.FSM{}, fmt.Errorf("oneOrMore must be passed a string of length 1")
	}

	initial := "start"
	accept := s
	event := fsm.Event{
		From:  "start",
		Input: s,
	}
	eventAccept := fsm.Event{
		From:  s,
		Input: s,
	}
	events := make(map[fsm.Event]string)
	events[event] = accept
	events[eventAccept] = accept

	return fsm.NewFSM(initial, events, []string{accept}), nil
}
```

The implementation makes use of Go's bubble-it-up error handling convention to fail the match if either an `UnmatchedEventError` comes up due to a missing transition (i.e. a different letter than the character matched is input) or if the `CurrentState` of the FSM is not in the `AcceptStates` slice (for this FSM, only if passed the empty string, which leaves it in the initial state):

```go
func checkMatch(f *fsm.FSM, s string) bool {
	// checkMatch returns true if the given FSM matches the string
	// or false if it does not (e.g. f.Accepted != true or get an error from processString)
	err := processString(f, s)
	if err == nil {
		return f.Accepted()
	}
	return false
}
```

Writing out more any regex directly in terms of FSM states like this is not really practical but it can be fun to puzzle the state machine yourself to get to thinking about the theory behind a common tool.
