# Implicit Arguments

An implicit argument of a function is an argument which can be inferred from contextual knowledge.
There are different kinds of implicit arguments that can be considered implicit in different ways.
There are also various commands to control the setting or the inference of implicit arguments.

```coq
(* type A is inferrable by argument of cons *)
Inductive list {A : Type} : Type :=
| nil : list
| cons (x : A) (l : list) : list
.
```

## Maximal & Non-Maximal Insertion

When a function is partially applied and the next argument to apply is an implicit argument, the application can be interpreted in two ways.
If the next argument is declared as maximally inserted,
the partial application will include that argument.
Otherwise, the argument is non-maximally inserted and the partial application will not include that argument.

Each implicit argument can be declared to be inserted maximally or non maximally.
In Coq, maximally inserted implicit arguments are written between curly braces "{ }" and non-maximally inserted implicit arguments are written in square brackets "[ ]".

## Trailing Implicit Arguments

An implicit argument is considered trailing when all following arguments are implicit.
Trailing implicit arguments must be declared as maximally inserted;
otherwise they would never be inserted.
