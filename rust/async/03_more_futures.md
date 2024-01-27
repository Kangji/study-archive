# Typical Asynchronous Items

`futures` crate not only wraps the standard `future` but also provides many useful items and extensions.

Useful items are such as `Stream`, `Sink`, and I/O items.

Extensions are such as `Join`, `FusedFuture`.

## Stream

If `Future<Output = T>` is an asynchronous version of T, then `Stream<Item = T>` is an asynchronous version of `Iterator<Item = T>`.
A stream represents a sequence of value-producing events that occur asynchronously to the caller.

The trait is modeled after `Future`, but allows `poll_next` to be called even after a value has been produced, yielding `None` once the stream has been fully exhausted.

## Sink

A Sink is a value into which other values can be sent, asynchronously.

Basic examples of sinks include the sending side of:

* Channels
* Sockets
* Pipes

In addition to such “primitive” sinks, it’s typical to layer additional functionality, such as buffering, on top of an existing sink.

Sending to a sink is “asynchronous” in the sense that the value may not be sent in its entirety immediately.
Instead, values are sent in a two-phase way: first by initiating a send, and then by polling for completion.
This two-phase setup is analogous to buffered writing in synchronous code, where writes often succeed immediately, but internally are buffered and are actually written only upon flushing.

In addition, the `Sink` may be full, in which case it is not even possible to start the sending process.

As with `Future` and `Stream`, the `Sink` trait is built from a few core required methods, and a host of default methods for working in a higher-level way.
The `Sink::send_all` combinator is of particular importance: you can use it to send an entire stream to a sink, which is the simplest way to ultimately consume a stream.

## Asynchronous I/O: `AsyncRead` & `AsyncWrite`

`AsyncRead` and `AsyncWrite` are asynchronous versions of `Read` and `Write`.
They provide an asynchronous `poll_read` and `poll_write` method.
