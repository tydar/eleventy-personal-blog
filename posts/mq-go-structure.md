---
title: "Structure of MQ Go"
date: 2021-11-08
tags:
    - golang
layout: layouts/post.njk
---
One thing I really enjoy about coding in Go in comparison to Python or Javascript is the presence of explicit type signatures. Having concisely defined struct types makes Go code expressive and provides strong hints toward the way pieces of the code should connect to one another.

Here are the key types for MQ Go:

```go
type Queue struct {
   sync.Mutex
   queue []string
}
```

The `Queue` type is simple: a slice of `string`s and a mutex to allow the main queue to be written to by goroutines handling incoming messages and (destructively) read from by the distribution management goroutine through the function `func (q *Queue) Pop() (string, error)`.

```go
type Connections struct {
    sync.Mutex
    last    int
    writers map[int]string
    readers map[int]string
}
```

The `Connections` type is a wrapper for a mutex to manage access for potentially concurrent handler goroutines for new connections and disconnections, as well as a self-incrementing index `last`, and a pair of `map[int]string`s that allow lookup of registered clients.

Finally we have the main type `Server`:

```go
type Server struct {
    Queue       *Queue
    Connections *Connections
    JobCount    int
    Jobs        chan MsgJob
}
```

This wraps the `Queue` and `Connections` objects, as well as a job counter for completed message distributions for the rudimentary metrics dashboard, and a channel of type `MsgJob` to act as a buffered task queue for distribution worker goroutines.

For good measure, the `MsgJob` type:

```go
type MsgJob {
    JobID int
    Msg   string
    Dests map[int]string
}
```

This wraps a copy of the `readers` map from `Connections` so each worker goroutine has a local copy, a `JobID` to refer to the job, and a copy of the message to be distributed.

Explicit and concise type defintions like these help to structure the code -- the way they are written help me kick-start my thinking for the implementation code itself.
