# Lifetime

To ensure that all references do not outlive its value, Rust borrow checker uses lifetime concept.
Every references have their lifetime, which is the scope for which that reference is valid.
It is slightly different with the scope of a variable due to move or leak.

Rust borrow checker ensures that all borrows are valid - lifetime of reference is included in the value's scope.
In most case, the borrow checker could infer the lifetime, but not every case.
Even worse, the borrow checker could infer the wrong lifetime.
At that moment we need to explicitly annotate the lifetime via generic lifetime parameters.
Lifetime annotation is simply an explicit name of the lifetime and could be used in different context.
Therefore it is similar to generic type parameter.

Structs with reference fields require lifetime annotation on them.

## Static Lifetime

Static lifetime is special since it is a reference that can live forever.
An example is a string literal - they are stored in data section and available anywhere.

## Lifetime Bound

Lifetime bound is a relationship or comparison between two or more lifetimes.
If `'a: 'b`, then lifetime `'a` must outlive `'b`.
If `T: 'a`, then all lifetime of references in the struct must outlive `'a`.
If T is an owned type, then the lifetime bound holds for arbitrary lifetime, even static lifetime.
