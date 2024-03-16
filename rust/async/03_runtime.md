
# Asynchronous Runtime

Tasks are managed by rust runtime: runtime scheduler assigns multiple tasks to a single thread or multiple threads to execute, based on the runtime configuration.
Some of them might even support work stealing or load balancing.

## Executor

Future executors then take a set of top-level futures to create a tasks and run them to completion by calling `poll` whether the future can make progress.
Therefore, creating a task is dependent to a runtime implementation.
Typically, an executor will poll a future once to start off.

When futures indiciate that they are ready to make progress by calling `wake`,
scheduler place them back onto a queue and `poll` is called again.

## Scheduler

Runtime scheduler manages the list of tasks that are ready to be polled.
The behavior of task and scheduler resembles the traditional thread model.
Once the task is created or waken by `Waker`,
they are placed on the ready queue.

# Tokio: Rust Asynchronous Runtime Standard

Tokio is an asynchronous runtime for the Rust programming language.
It provides the building blocks needed for writing networking applications.
It gives the flexibility to target a wide range of systems, from large servers with dozens of cores to small embedded devices.

At a high level, Tokio provides a few major components:

* A multi-threaded runtime for executing asynchronous code.
* An asynchronous version of the standard library.
* A large ecosystem of libraries.

## When Not to Use Tokio

Although Tokio is useful for many projects that need to do a lot of things simultaneously, there are also some use-cases where Tokio is not a good fit.

* Speeding up CPU-bound computations by running them in parallel on several threads.  
Tokio is designed for IO-bound applications where each individual task spends most of its time waiting for IO. If the only thing your application does is run computations in parallel, you should be using rayon. That said, it is still possible to "mix & match" if you need to do both.
* Reading a lot of files.  
Although it seems like Tokio would be useful for projects that simply need to read a lot of files, Tokio provides no advantage here compared to an ordinary threadpool. This is because operating systems generally do not provide asynchronous file APIs.
* Sending a single web request.  
The place where Tokio gives you an advantage is when you need to do many things at the same time. If you need to use a library intended for asynchronous Rust such as reqwest, but you don't need to do a lot of things at once, you should prefer the blocking version of that library, as it will make your project simpler. Using Tokio will still work, of course, but provides no real advantage over the blocking API.

## Spawning

A task can be created using `tokio::task::spawn()`. The function accepts a future, which will work as a main future(top-level future) of the task.

Since the submitted future will run on separate task,
the future is required to be bounded by a static lifetime.
Which means, all values held by the future must be `'static`.

Since the task might run on different thread(depend on scheduling policy),
the submitted future is required to implement `Send`.
Which means, all values held by the future must be `Send`.

The function will return `JoinHandle<T>` which is analogous to `pthread_t *`.

`JoinHandle` is also a future,
which returns `Poll::Ready(Result<T, JoinError>)` iff the task is finished.

There are two reasons of join error. One is when the task panics, and the other is when the user aborted the task by `abort(&self)`.

## Synchronization

Tokio provides task level synchronization primitives, such as `Mutex`.
Most of primitives in `std::sync` are implemented as an asynchronous version in `tokio::sync`.

However, using `tokio::sync::Mutex` is often not a good choice, since it is too heavy and it is fine to use blocking mutex, as long as contention remains low and the lock is not held across calls to `.await`.

If contention on a blocking mutex becomes a problem, there are several options.

* Spawn a dedicated task to manage state and use message passing.
* Shard the mutex.
* Use non-blocking mutex (worst option).

### Channels

Channels are a producer-consumer based message queue.
Tokio provides multiple channels

* mpsc: multi-producer, single-consumer channel.
* oheshot: one-time used single-producer, single-consumer channel.
* broadcast: multi-producer, multi-consumer channel.
* watch: single-producer, multi-consumer channel.
