---
title: "Lessons from writing Stomper"
date: 2021-12-19
tags:
    - golang
    - stomp

layout: layouts/post.njk
---

# Lessons from writing Stomper

[Stomper](https://github.com/tydar/stomper) is my Go message broker project implementing the [Simple Text Oriented Messaging Protocol (STOMP)](https://stomp.github.io/). It's not *quite* fully compliant yet, but here are a few things I learned writing it.

1) Channels and goroutines are extremely powerful and intuitive.

I have stayed far away from working with concurrency primitives in most other programming languages I've learned. They always seemed a little bit like deep magic. Channels and goroutines seem obvious. There is little complexity in starting a new goroutine--a lot of cognitive overhead present in other languages is absent. Channels handle the simplest case of direct communication between two goroutines with ease.

I am very curious to learn more about how goroutines are implemented and how the scheduler is implemented--but not having to know any of that to start writing concurrent code is extremely helpful.

2) Define internal models for the interaction between software components.

In this project more than many others I made a strong conscious effort to build a decoupled or easily decoupled product. I think I was partially driven to this by the subject (a message broker is inherently about decoupling software), partially driven by inspiration from by other message brokers whose source code I reviewed, and finally motivated by a hope to work on this project in the longer term to expand its capabilities.

Attempting to build this more-decoupled project meant defining clear models for communication between components. This meant additional lines of code -- but many of those felt obvious. The difficult work was designing the interface between pieces before actually writing the code.

3) Simple containerization

I do not have a ton of experience with containerizing software. What experience I do have has been primarily Python. Though this is more an issue with the state of package management in Python and my experience level as a hobbyist, I have frequently ran into problems writing a working Dockerfile for a Python program that just did not come up creating Docker images in this project.

The Go toolchain is excellent and compiling to a static binary is also excellent.

4) net is a great package

I love the `net` package. Working with lower-level networking in other languages has always been a bit confusing to me. Having `net` provide a simple set of interfaces and a limited set of necessary methods helped me understand and implement TCP networking features quickly and easily.

5) Writing to spec is nice

In many projects I've taken on over time, I have defined the spec as I've gone. I might narrow to a specific minimum crucial feature set at some point, but I've been guilty of sprawl as well. Working to the spec has helped me prioritize and orient my work more easily.

On the other hand, writing to spec is a little intimidating--there's an exact standard and the language is not always exactly smooth. I appreciated choosing a _small_ spec to implement. The set of total required features is not so large to be overwhelming and the absolute pagecount to comb through is small.

I'm very excited to finish up writing to the spec and implement one or two "nice to have" features for my project. Finally I'll say it again: I love static typing! Compile-time errors are much clearer and runtime errors have a narrower list of possible causes and I just write the right code the first time more often.

