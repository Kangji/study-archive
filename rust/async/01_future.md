# Future

A future represents an asynchronous computation obtained by use of `async`.
A future is a value that might not have finished computing yet.
This kind of “asynchronous value” makes it possible for a thread to continue doing useful work while it waits for the value to become available.

## `Poll` Method

The core method of future, `poll`, attempts to resolve the future into a final value.

When using a future, you generally won’t call `poll` directly, but instead `.await` the value.

## Waker

When a future is not ready yet, poll returns `Poll::Pending` and stores a clone of the `Waker` copied from the current `Context`.
This Waker is then woken once the future can make progress.
For example, a future waiting for a socket to become readable would call `.clone()` on the Waker and store it.
When a signal arrives elsewhere indicating that the socket is readable, `Waker::wake` is called and the socket future’s task is awoken.
Once a task has been woken up, it should attempt to poll the future again, which may or may not produce a final value.

## Executor

Future executors take a set of top-level `Futures` and run them to completion by calling poll whenever the Future can make progress.
Typically, an executor will poll a future once to start off.
When Futures indicate that they are ready to make progress by calling `wake()`, they are placed back onto a queue and poll is called again, repeating until the Future has completed.

Various asynchronous runtime such as `tokio` provides executors.
