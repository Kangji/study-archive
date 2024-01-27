# Ownership

One of Rust philosophies is memory safety without garbage collection.
In most languages without a GC, it’s our responsibility to identify when memory is no longer being used and to call code to explicitly free it, just as we did to request it.
Doing this correctly has historically been a difficult programming problem.
If we forget, we’ll waste memory (memory leak).
If we do it too early, we’ll have an invalid variable (dangling pointer).
If we do it twice (double free), that’s a bug too.
We need to pair exactly one allocate with exactly one free.

To accomplish this, Rust introduces new concept, ownership.
By ownership rule, every value(and it's memory) has its unique owner.
Exactly one variable owns the value, and when the context exits the scope of the variable, its resources are deallocated.
Since the scope is known at compile-time, compiler knows when the value is dropped and then deallocates the resources.

Therefore all allocated resources are deallocated exactly once - safe from double-free and memory leak issue.

## Move

However rust still undergoes dangling pointers.
For example, when more than one variable refers to a heap-allocated value (by holding its pointer), then whichever variable owns the value, the other become dangling pointer when the context exits the scope.

Rust solve this by invalidating the variable when it loses the ownership of its value.

```rs
let x: String = todo!();
let y = x;
```

In the example above, a new variable `y` refers to the string stored in the heap, which is also referenced by `x`.
This is an example of shallow copy, in other programming languages.
However in Rust, the variable `x` is no longer valid so that the variable `y` is the only valid variable that refers to the string.
This is called `move`: the value has been moved from `x` to `y`.

Therefore all values can only referenced by exactly one variable (don't think about reference types for now) - safe from dangling pointer issue.
We'll talk about reference type, or just reference at next chapter.

## `Copy` Trait

If a value is stored only in stack memory, there is no reason to invalidate the variable when it is assigned to another variable.
Since it is stored in the stack, it is actually copied into somewhere else in the stack, and then new variable is binded to that value.
Rust provides `Copy` trait so that the move does not occur and copies the value instead.
All primitive types implement `Copy` trait by default.
