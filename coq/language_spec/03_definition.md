# Definition

## Let-in Definitions

```coq
let name {binder}? {: type}? := term in term

let x : nat := S O in O + x = x.
let addone (n : nat) : nat := S n in addone n = S n.
(* which expands to *)
let addone : nat -> nat := fun (n : nat) => S n in addone n = S n.
```

일반적인 ML언어에서 let-in expression.
Represents the local binding of the variable.

## Top-Level Definition

```coq
Definition ident binder* (: type)? := term.
Example ident (binder)* (: type)? := term.

Definition andb (b1 : bool) (b2 : bool) : bool :=
  match b1 with
  | true => b2
  | false => false
  end.

Definition andb : forall (b1 : bool) (b2 : bool), bool :=
  fun b1 : bool =>
    fun b2 : bool =>
      match b1 with
      | true => b2
      | false => false
      end.

Definition andb : bool -> bool -> bool :=
  fun b1 : bool =>
    fun b2 : bool =>
      match b1 with
      | true => b2
      | false => false
      end.

Example true_and_false_is_false : (andb true false = false).
```

Definitions extend the global environment by associating names to terms.
A definition can be seen as a way to give a meaning to a name or as a way to abbreviate a term.
In any case, the name can later be replaced at any time by its definition.

The operation of unfolding a name into its definition is called `delta-reduction`.
A definition is accepted by the system if and only if the defined term is well-typed in the current context of the definition and if the name is not already used.
The name defined by the definition is called a constant and the term it refers to is its body.
A definition has a type, which is the type of its body.

If term is omitted, type is required and Coq enters proof mode.
This can be used to define a term incrementally,
in particular by relying on the refine tactic.
In this case, the proof should be terminated with Defined in order to define a constant for which the computational behavior is relevant.
Therefore we usually use `Example` instead of `Definition`,
though they are semantically and syntactically identical.

## Top-Level Fixpoint

```coq
Fixpoint plus (n : nat) (m : nat) : nat :=
  match n with
  | O => m
  | S n' => S (plus n' m)
  end.

(* This is equivalent to *)
Definition plus : nat -> nat -> nat :=
  fix self n : nat :=
    fun m : nat =>
      match n with
      | O => m
      | S n' => S (self n' m)
      end.
```

No more than a syntactic sugar.

## Assertions and Proofs

An assertion states a proposition (or a type) for which the proof (or an inhabitant of the type) is interactively built using tactics (via proof mode).
Assertion cause Coq to enter proof mode.
Following `Proof` command is optional, but recommended.

In most case assertion tokens are semantically equivalent. (Use either one)
Besides, they are fundamentally no more than `Definition` command.

For example,

```coq
Theorem test_eq (n : nat) : n = n.
(* is equivalent to *)
Definition test_eq : forall n : nat, n = n. intros.
```

There are some constraint, which means that `Definition` command is more flexible.

* Assertion cannot directly provide element. It must use tactics. Nevertheless, all tactics represents the term or part of the term.
