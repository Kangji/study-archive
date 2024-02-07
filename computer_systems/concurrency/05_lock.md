# Lock

Lock is a synchronization variable that provides mutual exclusion – when one thread holds a lock, no other thread can hold it.
Therefore lock is also called as "mutex".

A program associates each lock with some subset of shared state and requires a thread to hold the lock when accessing that state.

## Lock API

Two states: BUSY or FREE

* acquire: Wait until lock is FREE, then atomically makes BUSY
* release: Makes lock FREE so that other threads can acquire.

`acquire` method is atomic - only one thread can execute at once.
`release` must be called by the thread that acquired lock.

## Critical Section

Segment of codes that requires atomically accessing shared states or resources.
At most one thread can be in critical section at once.
Therefore, protect(guard) the critical section with mutex => RAII

## Properties

* Mutual Exclusion (Safety) - Only one thread can hold(acquire) lock at once
* Progress (Liveness) - If no thread holds the lock and any thread attempts to acquire the lock, then eventually some thread will success
* Bounded Waiting (Starvation-Free) - If thread T attempts to acquire a lock, then there exists a bound on the number of times other threads can successfully acquire the lock before T does

## Desirable Properties

* Efficient - Don't consume too much resource while waiting
* Fair - Don't make one thread wait longer than others
* Simple - Should be easy to use

## Read-Write Lock

Any number of threads can safely read shared data at the same time, as long as no thread is modifying the data.

* `read_lock`: acquires lock in read(shared) mode
  * FREE => success to acquire, change state to READ
  * READ => success to acquire
  * WRITE => wait until FREE or READ
* `write_lock`: acquires lock in write(exclusive) mode
  * FREE => success to acquire, change state to WRITE
  * READ => wait until FREE
  * WRITE => wait until FREE
