# Hoare Logic

## Big-Step Operational Semantics

Imperative program의 big-step operational semantics

* initial state에서
* 프로그램을 실행하면
* final state가 된다

따라서 big-step semantics에서 imperative program은 두 state와 command의 ternary relation이다.

## Hoare Triple

Hoare triple `{P} c {Q}`의 의미

* initial state에서 P가 성립하고 (precondition)
* c를 실행하고 어떤 final state에 도달했을 때
* final state에서 Q가 성립한다 (postcondition)

Formal definition: `forall P c Q st st', st =[ c ]=> st' -> P st -> Q st'`

### Examples of Valid Hoare Triples

* {X = 3} X := X + 1 {X = 4}
* {True} X := 3 {X = 3}

## Proof Rules

### Skip

```txt
------------
{P} skip {Q}
```

### Sequence

```txt
{Q} c2 {R}
{P} c1 {Q}
---------------
{P} c1 ; c2 {R}
```

premise의 순서가 반대인 이유는 보통 증명 과정이 postcondition에서 시작하기 때문.

### Assignment

```txt
------------------------
{Q [X |-> a]} X := a {Q}
```

`{Q [X |-> a]}`는 Q에서 모든 X를 a로 치환한 assertion을 말한다.

### Consequence

각 condition은 다른 condition보다 logically stronger 혹은 weaker일 수 있다.

명제 논리에서 P -> Q가 참이면 P는 Q보다 stronger, Q는 P보다 weaker.

마찬가지로 어떤 assertion P가 어떤 assertion Q를 imply하면,
즉 forall st, P st -> Q st이면, P는 Q보다 stronger.

```txt
Q' -> Q
{P'} c {Q'}
P -> P'
-----------
{P} c {Q}
```

### Conditionals

```txt
{P1} c1 {Q}
{P2} c2 {Q}
--------------------------------
{(b -> P1) /\ (~b -> P2)} if b then c1 else c2 end {Q}
혹은
{(b /\ P1) \/ (~b /\ P2)} if b then c1 else c2 end {Q}
```

### Loop

```txt
{P /\ b} c {P}
------------------------------
{P} while b do c end {P /\ ~b}
```

이 때 P는 loop invariant이다.

## Weakest Precondition

주어진 command와 postcondition에 대하여 가장 weak한 precondition (wp).
사실 위 skip, assignment, sequence, conditional에 대한 proof rule이 wp이다.

Loop에 대해서는 weakest precondition을 따지지 않는다.
