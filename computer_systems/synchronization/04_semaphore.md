# Semaphore

A synchronization variable that has a non-negative integer value
=> Must initialize with some value
=> At most n thread holds lock

Semaphore is dangerous => use other primitives

## Semaphore API

* P: waits for value to become positive then decrement
* V: atomatically increment value and wakes waiter

## Conditiion Variable using Semaphore

```cpp
void wait(lock_t *lock) {
    semaphore_t *sema = new Semaphore();
    wait_queue.append(sema);
    lock.release();
    sema.P();
    lock.acquire();
}

void signal() {
    if (!wait_queue.empty()) {
        semaphore_t *sema = wait_queue.pop();
        sema.V();
    }
}
```

## Semaphore with Condition Variable

```cpp
void P() {
    lock.acquire();
    while (!(value > 0)) {
        cond.wait(&lock);
    }
    value--;
    lock.release();
}

void V() {
    lock.acquire();
    value++;
    cond.signal();
    lock.release();
}
```
