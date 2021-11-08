---
title: "TIL: Go loops and closures"
date: 2021-11-08
tags:
    - til
    - golang
layout: layouts/post.njk
---
I have been working on a toy message queueing server and wrote this code to run a small stress test:

```go

func requestWorker(id int) {
    ...
}

func main() {
    var wg sync.WaitGroup
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            requestWorker(i)
        }()
    }
    wg.Wait()
}
```

`requestWorker(i)` sends a high number of requests to the server with each request body including the `id` passed as a parameter. Here it is the index variable from the loop. However, every request body was sent with an `id` equal to 10!

According to [this discussion from Stack overflow](https://stackoverflow.com/questions/10116507/go-transfer-var-into-anonymous-function), this happens because in my initial code Go the `i` variable is the index variable from the loop, and is being passed by reference because the anonymous function creates a closure.

Passing it as a parameter to the anonymous function instead allocates a new instance for that function call. I had to modify the code to instead say:

```go
func main() {
    var wg sync.WaitGroup
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(id int) {     // changed this line to include a parameter for the anonymous function
            defer wg.Done()
            requestWorker(id) // changed this line to use that parameter
        }(i)                  // changed this line to pass the current loop index as a parameter
    }
    wg.Wait()
}
```
