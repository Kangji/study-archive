# POSIX Thread API

```c
/**
 * Create thread and make ready state.
 * 
 * @name thread Pointer that points the thread
 * @name thread_routine Function to execute - void pointer type is type polymorphism
 * @name arg Arguments provided to function
 * @return Thread ID
 */
int pthread_create(pthread_t *thread, pthread_attr_t *attr, void *(*thread_routine)(void *), void *arg);

/**
 * Join the thread.
 *
 * @name thread thread to join
 * @name retval reference of variable to store return value
 */
int pthread_join(pthread_t thread, void **retval);
```
