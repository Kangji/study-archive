# Future

A future is any value that represents an asynchronous operation by storing its state of progress. Simply speaking, a future is a state machine.
This kind of “asynchronous value” makes it possible for a thread to continue doing useful work while it waits for the value to become available to proceed.

When there is an asynchronous operation, there must be a future corresponding to the operation.
Otherwise, it is impossible to know the current state of the operation,
since it is asynchronous.

## Poll

In code level, there is a `Future` trait which represents the future.
It provides `poll` method which returns whether the operation is ready to return or not.
Asynchronous operations are executed and attempt to resolve into a final value,
only when polled.

`poll` method returns `Poll::Pending` to yield when the future is not ready yet, for example, needs to wait for another asynchronous operation to be done.
Then the asynchronous runtime will move the task to waiting queue,
and assigns another task from ready queue to poll.
Logically, the task yields.

Futures are analogous to nested functions:
inner futures are polled by outer futures,
and the top-level futures, or tasks, are polled by asynchronous runtime.

Since the compile-time green threads are not preemptible,
the `poll` method should not block and return as quickly as possible.
Note that executing `poll` method only proceeds a part of the operation.

## Waker

When a future is not ready, `poll` returns `Poll::Pending` and stores the `Waker` copied from the `Context` of current task and call `Waker::wake()` when the future should be polled again.
Once a future has been woken up, it should be re-scheduled to attempt to poll the future again, which may or may not produce a final value.

## `async`/`.await`

Rust also provides a syntactic sugar for the futures.
Futures can be written by using `async` block, an async version of zero-argument closure.
The operation inside the `async` block is what the future executes when it is polled.

Futures defined by `async` block can nest other futures by `.await`ing on them.
When the future is `.await`ed, the future is consumed and pinned on the stack.
The caller will wait until the inner future resolves, then proceed.
Therefore, only using `async`/`await` on multiple futures will be executed sequentailly.

To achieve the concurrency of futures, you might either create a new task,
or multiplex futures on current task, via join or select.

## `async fn`

Asynchronous function is another syntactic sugar for `async` block.
An asynchronous function is simply a function that returns a future,
created by `async` block.

```rs
async fn hello(arg: ArgType) -> ReturnType {
    do_something(arg).await
}

// is identical to

fn hello(arg: ArgType) -> impl Future<Output = ReturnType> {
    async move {
        do_something(arg).await
    }
}
```

However, an asynchronous function provides more flexivity on return type,
in that `impl` type cannot specify another `impl` type as its associated type.
For example, `async fn foo() -> impl Clone` is possible while `fn foo() -> impl Future<Output = impl Clone>` isn't.

## Stream

If `Future<Output = T>` is an asynchronous version of `T`, then `Stream<Item = T>` is an asynchronous version of `Iterator<Item = T>`.
A stream represents a sequence of asynchronous value-producing events.

`Stream` provides `poll_next`, an asynchronous version of `next`.
Once the stream is exhausted, the method yields `None::<T>`.

## Sink

A sink is a value into which other values can be sent asynchronously.
Basic examples of sinks are the sending side of channels, sockets, or pipes.

In addition to such "primitive" sinks, it's typical to layer additional functionality, such as buffering, on top of an existing sink.

Sending value to a sink is "asynchronous" in the sence that the value may not be sent in its entirety immediately.
Instead, values are sent in a two-phase way: first by initiating a send, and then by polling for completion.
This two-phase setup is analogous to buffered writing in synchronous code,
whenre writes often succeed immediately, but internally are buffered and are actually written only upon flushing.

In addition, the `Sink` may be full, in which case it is not even possible to start the sending process.

As with `Future` and `Stream`, the `Sink` trait is built from a few core required methods, and a host of default methods for working in a higher-level way.
The `Sink::send_all` combinator is of particular importance: you can use it to send an entire stream to a sink, which is the simplest way to ultimately consume a stream.

## Async I/O

`AsyncRead` and `AsyncWrite` are asynchronous versions of `Read` and `Write`.
They provide an asynchronous `poll_read` and `poll_write` method.
