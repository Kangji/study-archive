# Typing

The underlying formal language of Coq is a Calculus of Inductive Constructions.

## Terms

The expressions of CIC are *terms* and all terms have a *type*.
There are types for functions (or programs),
there are atomic types (especially datatypes)...
but also types for proofs and types for the types themselves.

Terms are built from sorts, variables, constants, abstractions, applications, local definitions, and products.
From a syntactic point of view, types cannot be distinguished from terms,
except that they cannot start by an abstraction or a constructor.
More precisely the language of the CIC is built from the following rules.

1. The sorts `Set`, `Prop`, and `SProp` are terms.
2. Variables, hereafter ranged over by letters x, y, etc., are terms.
3. Constants, hereafter ranged over by letters c, d, etc., are terms.
4. If x is a variable and T, U are terms,
then `forall x : T, U` is a term.
It is a type, too.
5. If x is a variable and T, u are terms,
then `fun x : T => u` is a term.
However, it is not a type.
6. If t and u are terms then `t u` is a term. (Function application)

## Typing Rule

### Local Context

A local context is an ordered list of declarations of variables.
The declaration of a variable is either an *assumption* `(x : T)`,
or a *definition* `(x := t : T)`.

Local contexts are written in brackets (`[x : T; y := u : U; z : V]`).
The variables in a local context must be distinct.

Usually local context is denoted by $\Gamma$.
If x is an assumption in $\Gamma$, it is denoted by $x \in \Gamma$.
If x is a definition in $\Gamma$, it is denoted by $(x := t : T) \in \Gamma$.
$\Gamma::(y:T)$ or $\Gamma::(y:=t:T)$ or $\Gamma_{1};\Gamma_{2}$ are concatenation.

### Global Environment

A global environment is an ordered list of *declarations*.
Global declarations are either *assumptions*, *definitions* or declarations of inductive objects.
Inductive objects declare both constructors and inductive or coinductive types.

Assumptions are written as `(c : T)`, indicating that `c` is of the type `T`.
Definitions are written as `c := t : T`, indicating that `c` has the value `t` and type `T`.
We shall call such names *constants*.

### Rules

$E[\Gamma] \vdash t: T$ means that term t is well defined and has type T in the global environment E and local context $\Gamma$.

* Axiom-Prop : $E[\Gamma] \vdash \rm{Prop : Type(1)}$  
=> `Prop` has type `Type(1)`.

* Axiom-Set : $E[\Gamma] \vdash \rm{Set : Type(1)}$  
=> `Set` has type `Type(1)`.

* Axiom-Type : $E[\Gamma] \vdash \rm{Type(i) : Type(i + 1)}$  
=> `Type(i)` has type `Type(i+1)`.

* Product-Prop : if $E[\Gamma] \vdash T : s$, $s \in S$, $E[\Gamma :: (x : T)] \vdash U : \rm{Prop}$ then $E[\Gamma] \vdash \forall x: T, U : \rm{Prop}$  
=> `forall x : T, U` has type `Prop` if `T`'s type `s` is a simple sort and `U` has type `Prop`.  
(for example, forall x : nat, n = n)

* Product-Set : if $E[\Gamma] \vdash T : s$, $s \in \rm{\{Prop, Set\}}$, $E[\Gamma :: (x:T)] \vdash U : \rm{Set}$ then $E[\Gamma] \vdash \forall x : T, U : \rm{Set}$  
=> `forall x : T, U` has type `Set` if `T`'s type `s` is either `Set` or `Prop` and `U` has type `Set`.  
(for example, forall x : nat, nat)

* Product-Type : if $E[\Gamma] \vdash T : s$, $s \in \rm{Type(i)}$, $E[\Gamma :: (x:T)] \vdash U : \rm{Type(i)}$ then $E[\Gamma] \vdash \forall x : T, U : \rm{Type(i)}$  
=> `forall x : T, U` has type `Type(i)` if `T` has type `Type(i)` and `U` has type `Type(i)`.
(for example, `forall _ : Set, Set` has type `Type(1)`)
