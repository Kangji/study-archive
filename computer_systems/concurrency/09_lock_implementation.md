# Implementing a Lock

How to implement lock?

## Version 1: Disable Interrupt

* Simple
* Privileged instruction => Not available in user level
* Only works on uniprocessor

## Version 2: Software Lock (Peterson's Algorithm, N = 2)

```c
// Turn: Whose turn is it?
// Flag: Who wants to enter critical section?
// flag[self] = 1 && turn = self => Holding lock
// flag[self] = 1 && turn != self => Waiting to hold lock (if flag[1-self] = 0)
// flag[self] = 0 => Unlock
int turn = 0;
int flag[2] = {0, 0};

void lock() {
    flag[self] = 1; // I need lock
    turn = 1 - self; // Give away the turn
    // Spin Wait for my turn while other thread holds the lock
    while (flag[1 - self] == 1 && turn == 1 - self);
}

void unlock() {
    flag[self] = 0;
}
```

* N > 2 => Lamport's Bakery Algorithm
* Too hard
* InstructionReordering matters

## Version 3: Atomic Instruction

```c
void lock() {
    while (test_and_set(&flag)); // spin wait
    while (test_and_set(&flag)) yield(); // yield
}

void unlock() {
    flag = 0;
}
```

* Spin wait => Waste CPU cycle + Too much context switch
* Yield => Still too much context switch + Starvation Possible

## Version 4: Sleep Lock

* Lock itself is a shared object => Lock for Lock
* Do not avoid spin wait, but make wait time short
* `unlock` directly transfers lock to waiting thread

```c
typedef struct mutex_t {
    int flag; // Mutex state
    int guard; // guard to avoid losing wakeup
    queue_t *queue;
} mutex_t;

void lock(mutex_t *lock) {
    while (test_and_set(&lock->guard)); // spin wait 
    if (flag == 0) { // FREE
        flag = 1; // FREE -> BUSY
        lock->guard = 0;
    } else { // BUSY
        enqueue(lock->queue, self);
        lock->guard = 0;
        yield();
    }
}

void unlock(mutex_t *lock) {
    while (test_and_set(&lock->guard)); // spin wait
    if (empty(lock->queue)) { // No waiting thread
        flag = 0; // BUSY -> FREE
    } else { // Waiting thread => direct transfer
        thread_t *next = dequeue(lock->queue);
        wakeup(next);
    }
    lock->guard = 0;
}
```
