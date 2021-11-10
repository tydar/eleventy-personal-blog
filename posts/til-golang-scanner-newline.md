---
title: "TIL: Go bufio.Scanner only discards a single trailing newline"
date: 2021-11-10-2021
tags:
    - til
    - golang
layout: layouts/post.njk
---
When using a `bufio.Scanner` to iterate over a `io.Reader` using the default `ScanLines` as the `SplitFunc`, the Scanner will stop at each newline character. That is, consecutive newlines will result in empty tokens.

For example, the following code:

```go
package main

import (
    "bufio"
    "strings"
    "fmt"
)

func main() {
    test := "Line 1\nLine 2\n\nLine 3\n"
    r := strings.NewReader(test)
    scanner := bufio.NewScanner(r)
    output := 1
    for scanner.Scan() {
        fmt.Printf("Line %d: %s\n", output, scanner.Text())
        output++
    }
}
```

Produces the following output:

```
Line 1: Line 1
Line 2: Line 2
Line 3:
Line 4: Line 3
```
