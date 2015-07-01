---
layout: default
title: Helios 2.0 API Specifications
description: Helios 2.0 API specifications
keywords: helios 2.0, specs, helios specs
---

# Helios 2.0 APIs
The general workflow for developers intending to consume Helios 2.0 is as follows:

1. Define a Helios `IChannel` object, which represents a duplex channel for interacting with an arbitrary socket implementation (`ITransport`.) `IChannel`s can be configured and bootstrapped to support different transports with different types of configuration options, including TLS support.
2. An `IChannel` will contain an `IChannelPipeline`, which defines a duplex pipeline of various `IChannelHandler` implementations who will be invoked in sequential order during events emanating from or TO the socket, depending on how the `IChannel` object is called.
3. All operations called on the socket will themselves be asynchronous and will return a `ChannelTask`, which is really just a wrapper for `Task<ChannelContext>`.

Here's a diagram that illustrates what this duplex pipeline would look like under a typical scenario:

<div class="row">
	<div class="large-12 columns small-centered" style="text-align:center;">
			<img src="images/helios-pipeline-api.png" alt="Helios `IChannel` with `IChannelHandler` pipeline wrapped around a socket transport"/>
	</div>
</div>

## What sorts of things will `IChannelHandler` do?
An `IChannelHandler` implementation can be anything that occurs during the normal process of working with a socket:

* Decoding and encoding messages (framing;)
* Encrypting and decrypting messages;
* Serializing and deserializing messages;
* Authentication and authorization; and
* Performing any arbitrary application-level work with deserialized messages.

An `IChannelHandler` can be anything involved in the process of working with each remote host connected to the underlying `ITransport` - and Helios will provide a number of built-in `IChannelHandler` implementations for performing routine functions such as [message framing](http://www.codeproject.com/Articles/37496/TCP-IP-Protocol-Design-Message-Framing "TCP/IP Protocol Design: Message Framing").

## How many `IChannel` instances are created per `ITransport`?
In server-side scenarios, **`ServerChannel`s have the ability to create child `IChannel` instances for each remote host** - this allows server-side developers to write their `IChannelHandler` implementations for working with a single host, thus an `AuthenticationChannelHandler` only needs to worry about authenticating a single user, versus authenticating potentially thousands.

This is, fundamentally, a much simpler programming model.

On the client-side, a developer can open a single `SocketChannel` and it will have precisely one `IChannel` instance.

### `IChannel`s run on a single thread
Each `IChannel` instance processes one batch of messages at a time, which gives us the following benefits as developers:

1. We can maintain state that is unique to the remote host on the other end of the socket inside each `IChannelAdapter` implementation and have it automatically be thread-safe;
2. We can safely add or remove `IChannelHandler`s from the `IChannelPipeline` on a per-host basis; for instance, if a user authenticates we can remove the `AuthenticationChannelHandler` from the pipeline and replace it with a `AuthenticatedChannelHandler` going forward.

### `IChannel` won't begin reading new messages from the socket until its finished processing its last read
Consider the following scenario:

1. 200 bytes arrive on a TCP socket between reads, and each contains 10 framed messages (4 byte header + 16 byte message.)
2. The `IChannel` fires its `ChannelRead()` operation, which passes the 200 bytes to a `LengthFrameDecoder` (implements `IChannelHandler`) step in the `IChannelPipeline`.
3. The `LengthFrameDecoder` pulls each of the individual frames out of the 200 byte buffer and passes those messages *one call at a time* to the next `IChannelHandler` in the pipeline, a `JsonSerializer` step.
4. The `JsonSerializer` deserializes a message into a usable C# object, and calls the next step in the pipeline -a user-defined `IChannelHandler` implementation.
5. The user-defined `IChannelHandler` does something with the C# object passed into it from earlier, and the operation is complete for that individual message.
6. This process gets repeated 9 more times for each of the messages that were decoded by the `LengthFrameDecoder`.

Before we can read any additional messages for the socket, steps 2-3 will need to be