# Rust's Async Paradigm

Asynchronous rust is nothing more than compile-time green threading,
performed by a rust runtime such as `tokio`.
The unit of execution a.k.a. green thread or user-level thread,
are usually called `task`.

Tasks are managed by rust asynchronous runtime: runtime scheduler assigns multiple tasks to a single thread or multiple threads to execute, based on the runtime configuration.

A well-known problem of green threading also exists.
Whenever the task calls blocking operation, it blocks the whole thread, which takes away the advantages of green threading.

Tasks consists of multiple futures, an asynchronous version of conventional functions, since executing a task might involves an execution of another asynchronous operations.
In fact, a task corresponds to a top-level future, which might polls and waits for another nested future.
This structure of futures are analgous to nested function calls in a thread.
Therefore a future is a smallest unit of asynchronous programming.
Unless multiplexed, the futures in a single task are executed sequentially.

Although asynchronous programming is supported in many languages, some details vary across implementations.

* `Future`s are inert in Rust and make progress only when polled.
Dropping a future stops it from making further progress.
* Async is zero-cost in Rust, which means that you only pay for what you use.
Specifically, you can use async without heap allocations and dynamic dispatch,
which leads to great performance enhancement.
* No built-in runtime is provided by Rust standard library.
