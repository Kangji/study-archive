# Deadlock

Circular waiting for resources(CPU, disk space, memory, lock, etc.)

* Starvation: thread waits indefinitely, which includes deadlock.

## Example

```c
// Thread A         // Thread B
lock1.acquire();
                    lock2.acquire();
lock2.acquire();
                    lock1.acquire();
```

## Necessary Conditions for Deadlock

* Bounded Resources => if infinite, no need to wait
* No Preemption => ownership cannot be revoked until release
* Wait while holding
* Circular waiting

## Deadlock Dynamics

* Safe state: all possible sequence of acquires are possible to be granted
* Unsafe state: some sequence can result to deadlock
* Doomed state: all sequence leads to deadlock

## Deadlock Prevention

* Exploit or limit program behavior (Most Strict)
* Predict and prevent
* Detect and recover (Most Relaxed)
  * Only when possible to rollback, i.e., database transaction

### Exploit or Limit

* Bounded resources => provide enough resources
* No preemption => preempt
* Wait while holding => don't do that
* Circular waiting => don't do that (always acquire lock in specific order)

## Resource Allocation Graph

### Deadlock Detection

* Node: thread & resource
* Edge: wait/owned
  * thread->resource: waits for
  * resource->thread: owned by
* Cycle = deadlock

### Possible Deadlock Detection

* Node: resource to acquire
* Edge: order of acquire
  * a -> b: some thread acquires a then b
* Cycle: possible deadlock

## Banker's Algorithm

Idea: never enter unsafe state

* Resource vector: total number of each resources
* Allocation matrix: number of each resources acquired by each thread
  * Allocated vector: number of each resources acqured by any thread
  * Available vector = Resource vector - allocated vector
* Need matrix: number of each resources still need for each thread

Algorithm:

1. Find a row(thread) in need matrix which is less than available vector.
    - If no such row exists, eventual deadlock is possible.
    - If such a row exists, the the thread represented by that row may complete with does(available) resources.
2. Assumes that that thread has terminated. (acquired, executed, and released all required resources to available vector)
3. Repeat 1, 2 until all thread reached pretended termination.
    - If failed, the initial state was unsafe state.
