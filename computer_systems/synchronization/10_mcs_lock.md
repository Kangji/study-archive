# MCS Lock

What if locks are still mostly busy?
Not only does waiting time to acquire lock increase,
but also time to execute a critical section.

Why? Lock acquire interferes the lock release, due to atomic instructions.
When waiting threads executes atomic instructions,
that also delays release operation to free the lock.

## MCS (Mellor-Crummey and Scott) Locks

* Assign each waiting thread a separate memory location to spin-wait
* To release a lock, the bit is set for one thread, telling it that it is the next to acquire the lock
* Create a linked list of waiters using compare-and-swap
  * Front of the list holds the lock
  * New thread uses compare-and-swap to add to the tail

```cpp
 Node {
    Node *next;
    std::atomic<bool> need_to_wait;
}

McsLock {
    Node *tail = NULL; // end of the list, shared variable
    thread_local Node *self; // something like thread local variable
}

McsLock::acquire() {
    self->next = NULL;

    // Common pattern of using compare_and_swap:
    // 1. store current value on local variable
    // 2. try compare_and_swap
    // 3. if fail (which means local variable is not synced),
    //    refresh current value

    // Read current tail
    Node *old_tail = tail;
    // Tail might be changed or not
    while (!compare_and_swap(&tail, old_tail, self)) {
        // If someone else changed the tail, do it again.
        old_tail = tail;
    }

    // If old_tail = NULL, then I am the front of the list => acquired.
    if (old_tail != NULL) {
        // need to wait until I am front of the list
        self->need_to_wait.store(true, release);
        old_tail->next = self;
        // spin wait on (self->need_to_wait)
        // each thread spin wait on different self
        while (self->need_to_wait);
    }
}

McsLock::release() {
    // If self = tail, then I am the only one => make list empty and free lock
    if (!compare_and_swap(&tail, self, NULL)) {
        // spin wait on (self->next), which is per-thread value
        // each thread spin wait on different self
        while (self->next == NULL);
        // proceed to next thread
        self->next->need_to_wait.store(false, relaxed);
    }
}
```
