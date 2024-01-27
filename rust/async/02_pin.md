# Pin

Types that pin data to its location in memory.

It is sometimes useful to have objects(values) that are guaranteed not to move, in the sense that their placement in memory does not change, and can thus be relied upon.
A prime example of such a scenario would be building self-referential structs, as moving an object with pointers to itself will invalidate them, which could cause undefined behavior.

At a high level, a `Pin<P>` ensures that the pointee of any pointer type P has a stable location in memory, meaning it cannot be moved elsewhere and its memory cannot be deallocated until it gets dropped.
We say that the pointee is “pinned”.
Things get more subtle when discussing types that combine pinned with non-pinned data.

By default, all types in Rust are movable.
Rust allows passing all types by-value, and common smart-pointer types such as `Box<T>` and `&mut T` allow replacing and moving the values they contain: you can move out of a `Box<T>`, or you can use `mem::swap`.
`Pin<P>` wraps a pointer type P, so `Pin<Box<T>>` functions much like a regular `Box<T>`: when a `Pin<Box<T>>` gets dropped, so do its contents, and the memory gets deallocated. Similarly, `Pin<&mut T>` is a lot like `&mut T`. However, `Pin<P>` does not let clients actually obtain a `Box<T>` or `&mut T` to pinned data, which implies that you cannot use operations such as `mem::swap`.

It is worth reiterating that `Pin<P>` does not change the fact that a Rust compiler considers all types movable.
`mem::swap` remains callable for any T.
Instead, `Pin<P>` prevents certain values (pointed to by pointers wrapped in `Pin<P>`) from being moved by making it impossible to call methods that require `&mut T` on them (like `mem::swap`).

## Unpin

Many types are always freely movable, even when pinned, because they do not rely on having a stable address.
This includes all the basic types (like bool, i32, and references) as well as types consisting solely of these types.
Types that do not care about pinning implement the `Unpin` auto-trait, which cancels the effect of `Pin<P>`.
For `T: Unpin`, `Pin<Box<T>>` and `Box<T>` function identically, as do `Pin<&mut T>` and `&mut T`.

Note that pinning and Unpin only affect the pointed-to type `P::Target`, not the pointer type P itself that got wrapped in `Pin<P>`.
For example, whether or not `Box<T>` is Unpin has no effect on the behavior of `Pin<Box<T>>` (here, T is the pointed-to type).

## Pin-Projection

When working with pinned structs, the question arises how one can access the fields of that struct in a method that takes just `Pin<&mut Struct>`.
The usual approach is to write helper methods (so called projections) that turn `Pin<&mut Struct>` into a reference to the field, but what type should that reference have? Is it Pin`<&mut Field>` or `&mut Field`?
The same question arises with the fields of an enum, and also when considering container/wrapper types such as `Vec<T>`, `Box<T>`, or `RefCell<T>`. (This question applies to both mutable and shared references, we just use the more common case of mutable references here for illustration.)

It turns out that it is actually up to the author of the data structure to decide whether the pinned projection for a particular field turns `Pin<&mut Struct>` into `Pin<&mut Field>` or `&mut Field`.
There are some constraints though, and the most important constraint is consistency: every field can be either projected to a pinned reference, or have pinning removed as part of the projection.
If both are done for the same field, that will likely be unsound!

As the author of a data structure you get to decide for each field whether pinning “propagates” to this field or not.
Pinning that propagates is also called “structural”, because it follows the structure of the type.

### Non-structural Pinning

It may seem counter-intuitive that the field of a pinned struct might not be pinned, but that is actually the easiest choice: if a `Pin<&mut Field>` is never created, which means that there is no need to pin, nothing can go wrong!
So, if you decide that some field does not have structural pinning, all you have to ensure is that you never create a pinned reference to that field.

You may also `impl Unpin for Struct` even if the type of field is not Unpin.
What that type thinks about pinning is not relevant when no `Pin<&mut Field>` is ever created.

### Structural Pinning

The other option is to decide that pinning is “structural” for field, meaning that if the struct is pinned then so is the field.

This allows writing a projection that creates a `Pin<&mut Field>`, thus witnessing that the field is pinned.

However, structural pinning comes with a few extra requirements:

1. The struct must only be `Unpin` if all the structural fields are Unpin.
This is the default, but Unpin is a safe trait, so as the author of the struct it is your responsibility not to add something like `impl<T> Unpin for Struct<T>`.
2. The destructor of the struct must not move structural fields out of its argument.
Drop takes `&mut self`, but the struct or fields might have been pinned before.
You have to guarantee that you do not move a field inside your Drop impl.
3. You must make sure that you uphold the `Drop` guarantee: once your struct is pinned, the memory that contains the content is not overwritten or deallocated without calling the content’s destructors.
4. You must not offer any other operations that could lead to data being moved out of the structural fields when your type is pinned.
For example, if the struct contains an `Option<T>` and there is a take-like operation with type `fn(Pin<&mut Struct<T>>) -> Option<T>`, that operation can be used to move a T out of a pinned `Struct<T>` – which means pinning cannot be structural for the field holding this data.
