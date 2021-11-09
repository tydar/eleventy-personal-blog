---
title: "A toy message queueing server in Go"
date: 2021-11-08
tags:
    - golang
layout: layouts/post.njk
---
Over a few hours in the last week, I wrote [MQ Go](https://github.com/tydar/mq-go) as a toy message-queue-over-HTTP.

The server accepts and sends all messages as JSON objects in the body of HTTP requests.

Once an MQ Go server is started, clients can request to join as a receiver or sender. Clients provide a mode specification and a URL endpoint for messages from the server.

When a client POSTs a message to the server's `/send/` URL, that message is added to the backing queue. A scheduler pops messages off of this queue as they become available and as worker threads become available. Once a worker is available, that message is sent to all clients registered as receivers.

One pattern that really helps reason about my code is the [struct type for message passing](https://github.com/tydar/mq-go/blob/main/server.go#L248). Using a struct to wrap the values that need to be passed between goroutines helps to decouple logic between parts of the program for me.

To make this work, I had to learn a little bit more about concurrency in Go. I hadn't ever written any concurrent software before. Go's channels provide intuitive concurrency support for [many use cases](https://github.com/tydar/mq-go/blob/main/server.go#L142). Traditional primitives like [mutexes](https://github.com/tydar/mq-go/blob/main/server.go#L34) fill in in circumstances where channels might be cumbersome--like maintaining thread-safety on shared global state.

I think I will add a couple of features to MQ Go -- maybe the ability to create more than one queue, or the ability to require an acknowledgement of receipt. Otherwise I may also try to put together an implementation of [STOMP](https://stomp.github.io/) or some subset of that protocol in Go.

