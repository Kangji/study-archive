# Lock Contention

Multiple threads are trying to acquire lock.
If lock is a bottleneck, can we restructure the program?

## Fine-Grained Locking

Example: Hash Table => Per-bucket locking + RwLock for table (due to resize)

## Per-Processor Data Structure

Partition the shared data structure based on the number of processors

## Ownership Design Pattern

At most one thread owns an object at a time, i.e., Rust.

## Staged Architecture

Divides a system into multiple subsystems called stages.

* Each stage includes state private to the stage and a set of one or more worker threads that operate on that state
* Different stages communicate by producer-consumer channels
