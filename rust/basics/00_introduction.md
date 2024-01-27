# Forewords

It wasn’t always so clear, but the Rust programming language is fundamentally about empowerment: no matter what kind of code you are writing now, Rust empowers you to reach farther, to program with confidence in a wider variety of domains than you did before.

Take, for example, “systems-level” work that deals with low-level details of memory management, data representation, and concurrency. Traditionally, this realm of programming is seen as arcane, accessible only to a select few who have devoted the necessary years learning to avoid its infamous pitfalls. And even those who practice it do so with caution, lest their code be open to exploits, crashes, or corruption.

Rust breaks down these barriers by eliminating the old pitfalls and providing a friendly, polished set of tools to help you along the way. Programmers who need to “dip down” into lower-level control can do so with Rust, without taking on the customary risk of crashes or security holes, and without having to learn the fine points of a fickle toolchain. Better yet, the language is designed to guide you naturally towards reliable code that is efficient in terms of speed and memory usage.

Programmers who are already working with low-level code can use Rust to raise their ambitions. For example, introducing parallelism in Rust is a relatively low-risk operation: the compiler will catch the classical mistakes for you. And you can tackle more aggressive optimizations in your code with the confidence that you won’t accidentally introduce crashes or vulnerabilities.

But Rust isn’t limited to low-level systems programming. It’s expressive and ergonomic enough to make CLI apps, web servers, and many other kinds of code quite pleasant to write — you’ll find simple examples of both later in the book. Working with Rust allows you to build skills that transfer from one domain to another; you can learn Rust by writing a web app, then apply those same skills to target your Raspberry Pi.

## Terminology

### Value

Value is an entity represented by a set of bits.

### Variable

Variable is an abstract container for a value.

Variables can be referenced by one or more identifier: in most case only referenced by a single identifier. In this case it is called variable name.

### Variable Binding

Association between variable name and variable (or value stored in variable).

### Scope

Valid range of variable binding.

Compiler blocks the access to the variable via its name out of its scope.

### Type

The way value or value stored in variable might be resovled as.

### Reference

Conceptually, any value that refers to the variable and its value, such as memory address, are called reference of a variable or value.

All values are either referential or non-referential value.

### Resource

Any sort of hardware resources.
In most case it refers to memory.
