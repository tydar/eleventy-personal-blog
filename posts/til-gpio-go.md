---
title: "TIL: Interact with GPIO on Raspberry Pi with Go"
date: 2022-01-05
tags:
  - til
  - pi
  - golang

layout: layouts/post.njk
---

I got a new Raspberry Pi Zero 2 W for Christmas with a ModMyPi LED Christmas Tree. I haven't worked with any GPIO devices before so the interface is all new to me. ModMyPi provides [a set of Python scripts](https://github.com/modmypi/Programmable-Xmas-Tree) as examples.

After playing with those a bit, I wanted to write [some code in Go](https://github.com/tydar/mmpxmas-go). Enter the [go-rpio](https://github.com/stianeikeland/go-rpio) library. This allows easy access to GPIO state through regular Go--no C libraries in the background. An example:


```go
star := rpio.Pin(12)        // the star is controlled by thes state of the GPIO pin Rpi labels with index 12
star.Output()               // enable output for the pin
star.High()                 // set the state to high / turn the star LED on
time.Sleep(time.Second / 3) // wait for ~300ms
star.Low()                  // set the state to low / turn the star LED off
```

In my [examples written in Go](https://github.com/tydar/mmpxmas-go) I implemented similar patterns to the original MMP samples, as well as a Bitcoin price monitor as a simple API-backed indicator. I do want to disclaim that I am *not* a crypto guy, I just knew APIs were easily accessible!
