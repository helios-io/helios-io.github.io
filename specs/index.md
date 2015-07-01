---
layout: default
title: Specs Overview
description: Helios 2.0 technical specifications
keywords: helios 2.0, specs, helios specs
---

# Helios 2.0 Specifications							

## Why?
[Helios v1.4](http://www.nuget.org/packages/Helios/1.4.0 "Helios v1.4.0") and earlier achieved its goal of being able to provide a solid reactive API on top of the [CLR Socket](https://msdn.microsoft.com/en-us/library/system.net.sockets.socket.aspx) implementation, but it was developed in a rush and without clear goals initially.

Helios found its stride as a core piece of the infrastructure behind Akka.NET - it's function is to serve as high performance networking middleware to power stand-alone apps and other major pieces of .NET infrastructure.

In light of that, many of the design decisions made for Helios 1.x need to be revisited and redone in order to better serve those aims. Helios can be so much better than what it is as of v1.4.

The goal of Helios 2.0 is to fully realize the project's goals without any regard for backwards compatibility with previous versions. A full break from the technical debt of the previous iteration, but while retaining the hard lessons learned in its implementation.

## Overarching Goal
Helios's goal can be summarized as the following:

> To provide an asynchronous, high-performance, and reactive programming API in .NET on top of standard networking stacks with minimal overhead for both the CLR and Mono.

The paramount concern with Helios is being extensible enough to fulfill a variety of end-user needs while still offering blazing-fast performance. 

## Concerns
Our design concerns consist of two cross-cutting concerns across three layers.

<div class="row">
	<div class="large-12 columns small-centered" style="text-align:center;">
			<img src="images/helios2-design-layers.png" alt="Helios 2.0 Design Concerns"/>
	</div>
</div>

### Performance
Performance is a cross-cutting concern - it must be evaluated and tested at every level. Per some of the best practices recommended in *[Writing High-Performance .NET Code](http://www.amazon.com/gp/product/0990583430/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=0990583430&linkCode=as2&tag=jiidevsblo-20&linkId=CMQQ7W3DQSIO2ZJN">Writing High-Performance .NET Code</a><img src="http://ir-na.amazon-adsystem.com/e/ir?t=jiidevsblo-20&l=as2&o=1&a=0990583430)*, the Helios team will make a habit of setting, measuring, and documenting quantifiable performance goals with each release via the build server.

### Security
Helios 2.0 will have full support for [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security "Transport Layer Security") and, eventually, [DTLS](https://en.wikipedia.org/wiki/Datagram_Transport_Layer_Security "Datagram Transport Layer Security") for UDP.

### Developer API
Helios' goal is to simplify the process of socket programming by exposing a layered approach to reading and writing messages to and from the network. Therefore, in the spirit of the Netty project we will be introducing a pipeline-style API that makes it easy for developers to extend and implement their own handlers for each stage in the message-handling process.

> This will include full support for the [TPL](https://msdn.microsoft.com/en-us/library/dd460717(v=vs.110).aspx "Task Parallel Library (TPL)") inside individual `ChannelHandler` steps.

### Concurrency API
Part and parcel with Helios' goal of simplifying socket programming for .NET developers must also include making it feasible to support multiple concurrent socket operations simultaneously. Adding tunable degrees of parallelism inside Helios and exposing tools to make it easy to specify how Helios clients or servers should behave will continue to be a major portion of the Helios project in 2.0.

### I/O API
Helios 2.0 will use [`SocketAsyncEventArgs`](https://msdn.microsoft.com/en-us/library/system.net.sockets.socketasynceventargs) and all of the latest APIs in .NET 4.5 for performing asynchronous I/O across all socket implementations.