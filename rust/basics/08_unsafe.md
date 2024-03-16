# Unsafe Rust

Rust has a second language hidden inside it that doesn’t enforce memory safety guarantees: it’s called unsafe Rust and works just like regular Rust, but gives us extra superpowers.

Unsafe Rust exists because, by nature, static analysis is conservative.
Although the code might be okay, if the Rust compiler doesn’t have enough information to be confident, it will reject the code.
When the compiler tries to determine whether or not code upholds the guarantees, it’s better for it to reject some valid programs than to accept some invalid programs.
Be warned that you use unsafe Rust at your own risk:
if you use unsafe code incorrectly, problems can occur due to memory unsafety,
such as null pointer dereferencing.

It’s important to understand that `unsafe` doesn’t turn off the borrow checker or disable any other of Rust’s safety checks:
if you use a reference in unsafe code, it will still be checked.
You’ll still get some degree of safety inside of an `unsafe` block.

In addition, `unsafe` does not mean the code inside the block is necessarily dangerous or that it will definitely have memory safety problems:
the intent is that as the programmer, you’ll ensure the code inside an `unsafe` block will access memory in a valid way.

## Dereferencing Raw Pointer

We know that compiler ensures references are always valid.
Unsafe rust has *raw* pointer types that are similar to references.
`*const T` is immutable while `*mut T` is mutable.

Different from references and smart pointers, raw pointers:
* Are allowed to ignore the borrowing rules by having both immutable and mutable pointers or multiple mutable pointers to the same location
* Aren’t guaranteed to point to valid memory
* Are allowed to be null
* Don’t implement any automatic cleanup

We can create raw pointers in safe code.
We just can't dereference raw pointers outside `unsafe` block.

## Calling an Unsafe Function or Method

Unsafe functions and methods look exactly like regular functions and methods,
but they have an extra `unsafe` before the rest of the definition.
The `unsafe` keyword in this context indicates the function has requirements we need to uphold when we call this function, because Rust can’t guarantee we’ve met these requirements.

### Safe Abstraction over Unsafe Code

Just because a function contains unsafe code doesn’t mean we need to mark the entire function as unsafe.
In fact, wrapping unsafe code in a safe function is a common abstraction.
The function could be safe if it is properly implemented and does not violate memory safety, even if it contains unsafe code.

## Accessing or Modifying Mutable Static Variable

In Rust, global variables are called static variables.
However, if two threads are accessing the same mutable global variable,
it can cause a data race.

Therefore accessing and modifying mutable static variable is unsafe.
It's difficult to ensure there are no data race.

## Implementing Unsafe Trait

We can use `unsafe` to implement an unsafe trait.
A trait is unsafe when at least one of its methods has some invariant that the compiler can’t verify.
We declare that a trait is unsafe by adding the unsafe keyword before trait and marking the implementation of the trait as unsafe too.
