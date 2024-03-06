# Conversion Rules

두 term이 equal by definition이라는 것은 term A에서 term B로 convertible하다는 것이다.
CIC에서는 아래와 같은 conversion rule들을 적용하여 term을 변형시킨다.

## α-Conversion (Alpha)

Two terms are *α-convertible* if they are syntactically equal ignoring differences in the names of variables bound within the expression.

For example `forall x, x + 0 = x` is α-convertible with `forall y, y + 0 = y`.

## β-Reduction (Beta)

Eliminates `fun`.

β-reduction reduces a *beta-redex*,
which is a term in the form `(fun x => t) u`.
(Beta-redex is short for "beta-reducible expression",
a term from lambda calculus.)

## δ-Reduction (Delta)

Replaces a defined variable or constant with its definition.

δ-reduction replaces variables defined in local contexts or constants defined in the global environment with their values.
Unfolding means to replace a constant by its definition.

## η-Expansion (Eta)

Replaces a term `f` of type `forall a : A, B` with `fun x : A => f x`.

## ζ-reduction (Zeta)

Removes let-in definition.

## ι-Reduction (Iota)

Match and fix reduction together.

### Match-Reduction

Eliminates `match`.

### Fix-Reduction

Eliminates a `fix` with beta-redex.

```coq
fix add n m : nat :=
  match n with
  | O => m
  | S n' => S (add n' m)
  end.

======================

fun n m : nat =>
  match n with
  | O => m
  | S n' => S ((fix add n0 m0 : nat :=
                  match n0 with
                  | O => m0
                  | S n0' => S (add n0' m0)
                  end) n' m)
  end.
```
