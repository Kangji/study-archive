# Functions and Assumptions

## Binders

Constructions such as `fun`, `forall`, `fix` and `cofix` bind variables.

A binding is represented by an identifier.
If the bound variable is not used in the expression,
the identifier can be replaced by the symbol `_`.

When the type of a bound variable cannot be synthesized by the system,
it can be specified with the notation `(ident : type)`.
There is also a notation for a sequence of binding variables sharing the same type: `(ident+ : type)`.

A binder can also be any pattern prefixed by a quote, e.g. `'(x,y)`.

For example:

```coq
x: bool (* binds identifier x to bool type *)
b3 b2 b0 b1: bit (* binds four identifiers to bit type *)
```

Some constructors allow binding of a variable to a value (let-binder).
In this case, the notation is `(ident := term)`.
In a let-binder, only one variable can be introduced at the same time.
`(ident : type := term)` is also possible.

## Functions

```coq
fun ident : type => term
```

This term defines the abstraction of the variable `ident`,
of type `type`, over the term `term`.
It denotes a function that evaluates to the expression term
(e.g. `fun x : A => x` denotes the identity function on type A).

```coq
fun (x : A) (y : B) => term

fun x : A =>
  fun y : B => 
    term
```

The keyword `fun` can be followed by several binders.
However it is equivalent to an iteration of one-variable functions.

A type of function is denoted by `type -> type`,
while nested functions are denoted by `type -> type -> type`.

## Recursive Functions

```coq
fix ident binder := term
```

`fun`과 차이점은 재귀호출이 가능하다는 점, 그래서 local context에 identifier가 있다는 점.

## (Dependent) Products

```coq
forall ident : type1, type2
```

This term denotes the *product* of the *ident* of *type1* over *type2*.
If ident is used in type2, then we say the expression is a dependent product,
and otherwise a non-dependent product.

Type `forall _ : A, B` is equivalent to `A -> B` iff `B` is non-dependent to A.
(`A -> B` notation is defined as `forall _: A, B`)
However, regardless of dependency,
any term `f` of type `forall x : A, B` can be expanded into `fun x : A => f x`.
It means that `f` is applicable to `x : A`, returning the element of type `B`.
Therefore, `forall x : A, B` is a function `A -> B(x)`.

Remember that `fun`, `fix` terms are an element of function type,
while `forall` term **is** a function type.

The intention behind a dependent product `forall x : A, B` is twofold.
It denotes either the universal quantification of the variable `x` of type `A` in the proposition `B`,
or the functional dependent product from `A` to `B`.

```coq
forall (x : A) (y : B), C

forall x : A,
  forall y : B,
    C
```

Like function, the keyword `forall` can be followed by serveral binders.
However it is equivalent to an iteration of one-variable product.

A simple product is an element of `Set`.
In other words, all products are type.

## Assumptions

```coq
*Token* {({*ident*}+ : *type*)}+
```

Assumptions extend the global environment with axioms, parameters, hypotheses or variables.
It binds an `ident` to a `type`.

`Axiom`, `Conjecture`, `Parameter` and their plurar forms are equivalent.
`Hypothesis`, `Variable` and their plural forms are equivalent.
