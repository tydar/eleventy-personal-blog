---
title: "TIL: Managing configuration with Viper in Go"
date: 2021-12-13
tags:
  - til
  - golang

layout: layouts/post.njk
---
The Go module [Viper](https://github.com/spf13/viper) allows extremely quick implementation of configuration in Go programs.

Here's a short example:

```go
package main

import "github.com/spf13/viper"

func main() {
	viper.SetDefault("N", 1)

	viper.SetConfigName(".conf")
	viper.SetConfigType("json")
	viper.AddConfigPath(".")

	viper.SetEnvPrefix("N_CONF")
	viper.AutoEnv()

	viper.GetInt("N")
}
```

Just seven lines of code and your program will look for a configuration value in a file called `.conf.json` in the same directory the program is called from as well as in the environment under the key `N_CONF_N`. Viper makes it easy to set defaults, provide multiple options for configuration, and be confident you're getting what you want.
