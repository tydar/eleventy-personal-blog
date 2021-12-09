---
title: "TIL: go vet and staticcheck for linting and analysis"
date: 2021-12-08
tags:
    - til
    - golang
layout: layouts/post.njk
---
`go vet` is a command shipped with the Go toolchain that finds errors and issues not detected by the compiler. It uses a set of heuristics to scan Go packages. To vet your current package, you can just run `go vet` from a terminal while in the same folder as the package. The list of heuristics and deeper instructions for using the command can be found at [pkg.go.dev/cmd/vet](https://pkg.go.dev/cmd/vet).

Staticcheck is a linter provided as its own package. The easiest way to install it is through the `go install` command:

```shell
go install honnef.co/go/tools/cmd/staticcheck@latest
```

This installs the tool in your `$GOPATH/bin`. This runs its own suite of style rules and other static analysis to detect common problems with Go code. You can simply run it from the terminal by typing its command `staticcheck` while in the same folder as a Go package. More info at the [Staticcheck docs](https://staticcheck.io/docs/).
