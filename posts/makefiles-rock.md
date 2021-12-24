---
title: "Makefiles rock"
date: 2021-12-23
tags:
    - make
    - linux
layout: layouts/post.njk
---
Recently I've been working on a couple of projects that require somewhat longer commands to be run for testing or that
require a lot of different shorter commands to be run. It has been a long time since I was introduced to `make` and I am no guru
but `make` makes it *really* easy to keep some versioned commands and hints around. For example, in my tiny chat server [stomp-chat](https://github.com/tydar/stomp-chat) (made to test one of my other projects, a message broker), it is hardcoded to expect
certain behavior from the server. That is most easily done with a `docker run` command with several `-e` flags. But now
I can just `make run-server` and voila. And all it took was this:

```makefile
run-server:
	docker run -d -p 32801:32801 \
		-e STOMPER_HOSTNAME=0.0.0.0 \
		-e STOMPER_TOPICS=/channel/main \
		-e STOMPER_TCPDEADLINE=0 \
	ghcr.io/tydar/stomper:main
```

Similarly, I've been learning some MongoDB basics for a project at work and I created a rudimentary static site generator that
pulls content from a local mongo instance. Here's the Makefile I use to make sure I never have to fumble when I take a spare
minute to work on the project:

```makefile
run-mongo:
	docker run -d -p 27017:27017 --name mongo_test mongo:latest

stop-mongo:
	docker stop mongo_test

build:
	go build .

build-site: 
	./mdbssg

run-site:
	docker build -t mdbssg-test . && docker run --name mdbssg_test -d -p 8080:80 mdbssg-test

stop-site:
	docker stop mdbssg_test && docker container rm mdbssg_test
```

It saved me a ton of time especially to write out those short lines for `run-site` and `stop-site`. A lot has changed
about that project since I started it, so this particular Makefile isn't in use, but it really helped finish my first
iteration quickly without too much fussing over docker commands or wasted time when I'd forget to `rm` a container before
running a new one.

I know there's a *lot* more that can be done with `make`, but even this simple use is great for quickly getting things done!
