# Reference & Borrowing

Disallowing multiple variables to reference a single value will cause serious performance degradation and consistency issue - everytime the value must be cloned.
Rust introduces `reference` type which does not take the ownership - instead it "borrow"s the value.
Reference types do not take ownership, so the value is not dropped.

Reference types might dangle, so rust enforces the references to be accessed only before the value is dropped, by lifetime.
We'll talk about lifetime in the next chapter.

Mutable references are able to mutate, therefore the original value must also be mutable.
In addition, in order to avoid data race, Rust restricts to have multiple mutable references to a single value.
It is allowed to have multiple references only if all of them are immutable.
In fact, this is a huge trade-off between data consistency and programming flexibility.
