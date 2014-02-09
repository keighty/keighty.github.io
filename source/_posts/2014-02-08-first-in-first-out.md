---
layout: post
title: "first in, first out"
date: 2014-02-08 11:14:30 -0800
comments: true
categories: [message queue]
---
###Why use message queues?
Message queues offer the benefits of high reliability at the cost of performance. This is particularly useful for web tasks that may take longer than the typical HTTP request. Requests are persisted, acknowledged, and confirmed asynchronously. They are not dropped or timed out, no matter how long they take to process.
<!--more-->

###Asynchronous request processing with RabbitMQ
A program that sends messages is a **producer**. A producer drops a message onto a **queue** (like a mailbox). A **consumer** is a program that mostly waits to receive messages, and processes them when they arrive.

RabbitMQ provides message acknowledgments. An ack(nowledgement) is sent back from the consumer to tell the queue that a particular message has been received and processed, and that the queue is free to delete it

###Lets be fair
By default, RabbitMQ will dispatch messages equitably to all workers, and will not check first to see if the worker is already busy. You can ask the queue to only send messages to a worker if it is available to process a message.

Use ```channel.prefetch(1)``` to prevent dispatching a new message to a worker until it has processed and acknowledged the previous one. Instead, RabbitMQ will dispatch it to the next worker that is not still busy. One possible ramification of prefetching is that the queue can become backed up if all workers are busy with long processes, so watch your queue length!

###Useful Commands
* list active queues: ```sudo rabbitmqctl list_queues```
* list queues along with unacknowledged messages: ```sudo rabbitmqctl list_queues name messages_ready messages_unacknowledged```

###Resources
* [Queue based system architecture](http://www.amazon.com/gp/product/B00HNG8ZFQ/ref=kinw_myk_ro_title)
* [RabbitMQ in Action: Distributed messaging for everyone](http://manning.com/videla/)
* [RabbitMQ.com](http://www.rabbitmq.com/tutorials/tutorial-one-ruby.html)
* [Installation](http://www.rabbitmq.com/install-debian.html)
