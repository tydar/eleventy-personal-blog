---
title: "TIL: Go subtests"
date: 2021-12-06
tags:
    - til
    - golang
layout: layouts/post.njk
---
To handle tests that require shared setup, Go allows the use of subtests. The type `testing.T` that lives in the Go [testing](https://pkg.go.dev/testing) module has a `T.Run` method.

The `T.Run` method has this signature:

```go
func (t *T) Run(name string, f func(t *T)) bool
```

The first argument is a name for the subtest. The second argument is typically passed as an anonymous function as below:

```go
func TestMainTest(t *testing.T) {
    sharedResource := NewResource(some, arguments)

    t.Run("_FirstSubtest", func(t *testing.T) {
        // test here
    })

    t.Run("_SecondSubtest", func(t *testing.T) {
        // test here
    })

    sharedResource.TearDown()
}
```
