---
title: "Stomper transactions and atomicity"
date: 2022-01-18
tags:
  - STOMP
  - golang
  - pubsub
layout: layouts/post.njk
---
Currently on my STOMP protocol message broker project [Stomper](https://github.com/tydar/stomper) I am working to implement the STOMP transaction semantics. This includes a BEGIN frame that initiates a transaction with an ID, a COMMIT frame that commits that transaction, and an ABORT frame that aborts a transaction. This post is just thinking out loud about that.

The [STOMP specification](https://stomp.github.io/stomp-specification-1.2.html#BEGIN) says this about transactions:

> `BEGIN` is used to start a transaction. Transactions in this case apply to sending and acknowledging - any messages sent or acknowledged during a transaction will be *processed atomically* based on the transaction.

Emphasis mine.

I have had one implementation attempt that I aborted already: handle a `[]Frame` instead of `Frame` as the unit of work in the backing store and in the send logic. This fails to solve the problem for two reasons:

1) It provides only a weak guarantee of atomicity. Yes, since the entire transaction is committed as one slice, you won't *attempt* to send any frame until you send the whole slice. But you could still send a frame and fail to send others.

2) The entire transaction may not end up in the same destination queue, so I would still end up splitting the frames up and could end up sending the messages at wildly different times. This is because my destination queue type is currently set up as a `map[string]Frame` -- each destination has its own queue of messages.

Thinking about atomicity, my first thought is DB transactions: if the transaction fails to commit, the already-executed steps are rolled back. But that is not an option for a message broker within the STOMP protocol: you cannot unsend a message. Any cancellation logic would be an extension of the protocol or defined by the client systems own protocols.

But the language "processed atomically" in the specification suggests only a guarantee that the STOMP server waits to process the messages until the transaction is committed. It provides no guarantee about all messages even being successfully *sent*, much less received.

Sources of failure in message processing could be errors in the frames themselves or network failure. I would like to provide well-defined behavior for both cases.

Looking to the implementation I reference most often, [CoilMQ](https://github.com/hozn/coilmq), transaction frames are simply processed as any other frame after the COMMIT is received.

### The decision

I have a couple of options:

1) Take the minimal guarantee of atomicity of processing. Send transaction frames back through the main frame processing pipeline with some flag to direct them to the appropriate message queue. In this case, an error on an individual message would not stop the sending of other frames from that transaction. Additionally in the case of a network failure no special guarantees would be provided. Finally since the individual transaction frames may have different destinations and destinations are handled by a round-robin style logic currently, a message on a very backed up destination may send much later than one sent to a light destination.

2) Provide special logic for writing a transaction to its destinations. This could mean rewriting the backing store logic to keep all destinations in the same queue. This could mean providing a fully separate logic for sending frames committed by a transaction. This would add a lot of complexity.

3) Do something in-between. Keep the change to handle slices of Frames instead of individual Frames in the backing store and send logic, and add additional logic to split transactions into their destinations. This mitigates some of the risks outlined in (1) but doesn't require a fully separate pipeline for transactions or a fundamental change in the backing store logic.
