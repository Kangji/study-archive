# Object-Oriented Features

Rust also supports object-oriented programming - encapsulation.

## Trait

Trait is something like interface that defines the prototype of a behavior,
but different in that the traits are not type.

For example `Clone` trait defines the deep copy of an object.

### Trait Bound

Trait bound is a set of traits that the type must implement.

### Supertrait

Supertrait is similar to trait bound, but applies to a trait instead.

### Associated Type

Traits can have associated types.
They are similar to generic types, but totally different.
Generic types are a part of a trait - for example, `Trait<T>` and `Trait<S>` are different trait.
However, an associated type is a type placeholder that the implementor must specify the concrete type.

## Trait Object

Trait object is something like polymorphism.
It encapsulates the actual type and only exposes the trait implementing.

Unlike generic types, its concrete type will be determined at runtime.
It is called dynamic dispatch, compared to static dispatch.
Dispatch is a procedure of constructing vtable.

### Object Safety

In order to be a trait object, the trait must be **object-safe**.
Fundamentally object safety is the ability to construct a vtable - all dispatchable methods only have sized types as arguments and return types.

A trait is **object safe** if it has the following qualities.

* All supertraits must able to be object safe.
* `Sized` must not be a supertrait (since trait object is dynamically sized).
* It must not have any associated constants.
* It must not have any associated types **with generics**.
* All associated functions must either be dispatchable from a trait object or be explicitly non-dispatchable
    * Dispatchable functions are:
        * Not have any type parameters (although lifetime parameters are allowed).
        * Be a method that does not use Self except in the type of the receiver.
        * Have a receiver with one of the following types:
            * `&Self` (i.e. &self)
            * `&mut Self` (i.e &mut self)
            * `Box<Self>`
            * `Rc<Self>`
            * `Arc<Self>`
            * `Pin<P>` where P is one of the types above
        * Does not have a `where Self: Sized` bound.
    * Explicitly non-dispatchable functions require:
        * Have a `where Self: Sized` bound.

## About Inheritance

Rust do not support inheritance; instead you should use composition.
Inheritance has recently fallen out of favor as a programming design solution in many programming languages because it’s often at risk of sharing more code than necessary.
Subclasses shouldn’t always share all characteristics of their parent class but will do so with inheritance.
This can make a program’s design less flexible.
It also introduces the possibility of calling methods on subclasses that don’t make sense or that cause errors because the methods don’t apply to the subclass.
In addition, some languages will only allow single inheritance, further restricting the flexibility of a program’s design.
