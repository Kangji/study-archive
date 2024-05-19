# Condition Variable

A synchronization object that lets a thread efficiently wait for a change to shared state that is protected by a lock.

## Idea

Since lock is expensive operation, everytime acquire - check shared state - release is too inefficient.
We want to acquire lock only when the condition is true.

## Condition Variable API

* wait: Wait until the condition variable is signalled.
* signal: Takes one thread off the waiting list.
* broadcast: Takes all threads off the waiting list.

`wait` method atomically releases the provided lock and add the thread into the waiting list of condition variable.
The thread is now WAIT state, which is not schedulable.

When wakened, it reacquires the provided lock before returning from the wait call.

`signal` method has no effect when no thread are waiting.
Note that `signal` method only takes a thread off the list and change the state into READY state, and DOES NOT release lock.
Therefore when `wait` method returned, the predicate is not guaranteed to be true.
This is the reason why `wait` call is always wrapped by `while`, not `if`.
