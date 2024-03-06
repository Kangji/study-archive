# Basic Notations and Conventions

## Lexical Conventions

### Blanks

Space, newline, horizontal tab은 blank다.
Blank는 아무것도 아니지만 token의 separator다.

### Comments

Comments are enclosed between `(*` and `*)`. They can be nested.
They can contain any character.
However, embedded string literals must be correctly closed.
Comments are treated as blanks.

### Keywords

The following character sequences are keywords defined in the main Coq grammar.

```coq
_ Axiom CoFixpoint Definition Fixpoint Hypothesis Parameter Prop
SProp Set Theorem Type Variable as at cofix else end
fix for forall fun if in let match return then where with
```

## **Term**

Terms are the basic expressions of Coq.
Terms can represent mathematical expressions, propositions and proofs,
but also executable programs and program types.

Example:

```coq
(* below is a term.*)
fun (x: bool) =>
    match x with
    | true => false
    | false => true
    end

S O

andb true false

day -> day
```

## **Type**

Coq에서 모든 valid한 term들은 associated type이 존재한다.
이런 관계를 우리는 `x of type T`, `x belongs to T` 등으로 말하고 `x: T`라고 쓴다.

The Coq kernel is a type checker:
verifies that a term has the expected type by applying a set of typing rules.
If that's indeed the case, we say that the term is `well-typed`.

A special feature of the Coq language is that types can depend on terms.
Because of this, types and terms share a common syntax.

All types are terms, but not all terms are types.

Intuitively, types may be viewed as sets containing terms.
We say that a type is inhabited if it contains at least one term (i.e. if we can find a term which is associated with this type).
We call such terms witnesses.
Note that deciding whether a type is inhabited is undecidable.
This is related to `Prop` types.

Formally, types can be used to construct logical foundations for mathematics alternative to the standard "set theory": we call such logical foundations "type theories".
Coq is based on the Calculus of Inductive Constructions,
which is a particular instance of type theory.

`Check` command로 term의 type을 확인하거나 검증할 수 있다.

## Sentence

Coq documents are made of a series of sentences that contain commands or tactics,
generally terminated with a period and optionally decorated with attributes.

## Command

A command can be used to modify the state of a Coq document,
for instance by declaring a new object, or to get information about the current state.
By convention, command names begin with uppercase letters.

### Check

`Check` command로 term의 type을 확인하거나 term의 type을 주장할 수 있다.

### Compute

`Compute` command로 constant (defined by definition command) unfold할 수 있다.

### Search

`Search` command로 연관된 constant나 inductive type을 찾을 수 있다.

### Arguments

function argument 설정을 바꿀 수 있다.
(Implicit <-> Explicit)

### Locate

`Search`와 비슷한데 definition이 아니라 선언 위치를 알려준다.

### Show Proof

Proof mode에서 현재 Proof term의 모습을 알려준다.

### Module

Coq provides a module system to aid in organizing large developments.
We won't need most of its features, but one is useful:
If we enclose a collection of declarations between Module X and End X markers,
then, in the remainder of the file after the End,
these definitions are referred to by names like X.foo instead of just foo.
We will use this feature to limit the scope of definitions,
so that we are free to reuse names.

## Tactic

A tactic specifies how to transform the current proof state as a step in creating a proof.
They are syntactically valid only when Coq is in proof mode,
such as after a Theorem command and before any subsequent proof-terminating command such as Qed.

나중에 다루겠지만 모든 tactic들은 term 또는 그 일부로 나타낼 수 있다.
