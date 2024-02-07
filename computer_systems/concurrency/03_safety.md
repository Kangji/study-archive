# Thread Safety

A function is thread-safe iff it will always produce correct results when called repeatedly from multiple concurrent threads.

## Thread-Unsafe Class

* Class 1: Functions that do not protect shared variables
* Class 2: Functions that keep state across multiple invocations
* Class 3: Functions that return a pointer to a static variable
* Class 4: Functions that call thread-unsafe functions

## Reentrant Functions

A function is reentrant iff it accesses no shared variables when called by multiple threads. Therefore, requires no synchronization operation.

당연히 reentrant function은 thread-safe하다.
