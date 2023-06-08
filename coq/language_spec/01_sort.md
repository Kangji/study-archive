# Sorts

The types of types are called **sorts**.

Those types, whose elements are types, are called *universe* in type theory.

All sorts have a type and there is an infinite well-founded typing hierarchy of sorts whose base sorts are `Prop` and `Set`.

## Type (Universe)

`Prop` and `Set` themselves can be manipulated as ordinary terms.
Consequently they also have a type.

Because assuming that `Set: Set` leads to an inconsistent theory (Russel's Paradox),
the language of CIC must has infinitely many sorts.
There are, in addition to the base sorts,
a hierarchy of universes `Type(i)` for any integer `i≥1`.
The universe level is denoted by `i`.

Summary: `Prop: Type(1)`, `Set: Type(1)`, `Type(i): Type(i+1)`.

## Prop

`Prop`이라는 sort는 logical proposition들의 type이다.
예를 들어, `Prop` sort의 원소 `M`(type)은 어떤 한 logical proposition에 해당하고,
type `M`은 이 logical proposition의 증명에 해당하는 term들의 집합이다.
즉, 만약 어떤 object `m`이 `M`에 속한다면 `M`은 증명가능한 것이다.
혹은, 만약 `M`의 원소가 존재한다면 `M`은 증명가능한 것이다.
`M`과 같은 `Prop` sort의 원소들을 proposition이라고 한다.

proposition의 example: `O + n = O`

### Form

logical proposition을 표현한 term들을 form이라고 하고, form은 type이며 prop의 원소이다.
참고로, Coq에 내장된 prop은 **없다**. 기본 prop들은 `Coq.Init.Logic`에 정의되어 있다.

|Forms|Notation|Example|
|:-|:-|:-|
|True||`I`|
|False|||
|forall *ident*: *type*, *form*||`forall x: nat, eq nat x x`|
|forall _: *form1*, *form2*|*form1* -> *form2*||
|not *form*|~ *form*|`not True`|
|and *form1* *form2*|*form1* /\ *form2*|`and True False`|
|or *form1* *form2*|*form1* \\/ *form2*|`or (not False) True`|
|ex *specif* *term*|exists *ident*: *specif*, *form*|`@ex nat (fun x: nat => x = O)`|
|iff *form1* *form2*|*form1* <-> *form2*|`iff (x = y) (y = x)`|
|eq *type* *term1* *term2*|*term1* = *term2*|`@eq nat O O`|

### True & False

`True`와 `False`는 가장 기본이 되는 Prop type으로, inductive type이다.
Coq에서 proposition이 참일 필요충분조건은 원소가 존재하는 것이므로,
실제로 `True`와 `False`를 다음과 같이 정의한다.

```coq
Inductive True: Prop := I.
Inductive False: Prop :=.
```

### Implication

다음 문서에서도 다루겠지만, 임의의 type A, B에 대하여 `A -> B`는 `forall _: A, B`와 동치이다.
이는 당연히 proposition에서도 성립한다. 술어 논리 관점에서 보면 당연하다.

```coq
Notation "A -> B" := (forall _: A, B).
```

### Negation

`not`은 Prop A를 받아서 Prop `A -> False`를 return하는 함수이다.
따라서 `not`의 type은 `Prop -> Prop`이다.

```coq
Definition not: Prop -> Prop := fun A => A -> False.
Notation "~ A" := (not A).
```

여기서 우리가 아는 classical logic과 다른 점이 있다.
Classical logic에서는 임의의 prop P에 대하여 P이거나 not P이다.
즉, P가 참이거나 (P -> False)가 참이다(excluded_middle).
그러나 constructive logic에서는 이것을 증명할 방법이 없다.
쉽게 말해, P도 not P도 참이 아닐 수 있다.

### Conjunction

`and A B`는 inductive type으로 정의한다.
임의의 prop A, B에 대하여 `and A B`가 prop이므로 and는 `Prop -> Prop -> Prop`이 되겠다.

한편, type `alpha`의 원소로 inductive type `ind`의 원소를 만드는 constructor를 정의하면,
해당 constructor가 원소를 만들 필요충분조건은 `alpha`가 원소를 가지는 것이다.

`and A B`의 원소가 존재할 필요충분조건은 A와 B의 원소가 모두 존재하는 것이므로,
A와 B의 원소를 받는 유일한 constructor 만을 정의하면 되겠다.

```coq
Inductive and (A B : Prop) := conj : A -> B -> and A B
where "A /\ B" := (and A B).
```

### Disjunction

`or A B`또한 inductive type으로 정의한다.
임의의 prop A, B에 대하여 `or A B`가 prop이므로 or도 `Prop -> Prop -> Prop`이 되겠다.

`or A B`의 원소가 존재할 필요충분조건은 A 또는 B의 원소가 존재하는 것이므로,
A의 원소를 받는 constructor와 B의 원소를 받는 constructor 두 개를 정의하면 되겠다.

```coq
Inductive or (A B : Prop) :=
| or_introl : A -> or A B
| or_intror : B -> or A B
where "A \/ B" := (or A B).
```

### Existence

`ex A P` 또한 inductive type으로 정의한다.
임의의 type A와 `A -> Prop` P에 대하여 `ex A P`가 prop이므로,
ex는 `forall A : Type, (A -> Prop) -> Prop`이 되겠다.
참고로 `A -> Prop` 형태는 predicate이다.

`ex A P`의 원소가 존재할 필요충분조건은 `A`의 원소 x와 `P x`의 원소가 존재하는 것이므로,
A의 원소 x와 P x의 원소를 받는 유일한 constructor를 정의하면 되겠다.

```coq
Inductive ex (A : Type) (P: A -> Prop) : Prop :=
| ex_intro : forall x : A, P x -> ex A P.

Notation "exists x, P" := (ex _ (fun x => P)).
Notation "exists x : A, P" := (ex A (fun x : A => P)).
```

### Equivalence

`iff`는 Prop A와 B를 받아서 Prop `and (A -> B) (B -> A)`를 return하는 함수다.
따라서, `iff`의 type은 `Prop -> Prop -> Prop`이다.

```coq
Definition iff (A B : Prop) := and (A -> B) (B -> A).
Notation "A <-> B" := (iff A B).
```

### Equality

`eq A x y`는 주어진 A, x와 모든 y에 대한 inductive type으로 정의한다.
임의의 type A와 A의 원소 x, y에 대하여 `eq A x y`가 prop이므로,
eq의 type은 `forall A : Type, A -> A -> Prop`이 되겠다.

`eq A x`는 `eq A x x`만 원소가 존재하므로 `eq A x x`의 constructor만을 두면 되겠다.

```Coq
Inductive eq (A : Type) (x : A) : A -> Prop := eq_refl : eq A x x.
Notation "x = y :> A" := (eq A x y).
Notation "x = y" := (eq _ x y).
```

## Set

`Set`이라는 sort에는 `bool`, `nat`과 같은 작은 data type들부터 이들의 product, subset, 그리고 function type들까지 포함하는 sort이다.
예를 들어, `nat`, `nat -> nat`, `nat -> nat -> bool`은 모두 `Set`의 원소이다.

### Specification

Specification은 쉽게 생각하면 조건제시법으로 나타낸 `Set` sort의 원소로,
specification은 type이다.
예를 들어 `{x: nat | P x}`는 specification이다.
