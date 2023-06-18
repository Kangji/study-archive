# Inductively Defined Propositions

Coq에서 proposition은 귀납적으로 정의된다.
사실 모든 type은 귀납적으로 정의된다 (CIC).
이 때 해당 inductive type이 하나의 annotation을 가지면 predicate or property,
그리고 두 개 이상의 annotation을 가지면 relation이 된다.

annotation이 없는 경우는 사실 의미가 없다.
왜냐하면 prop은 원소 자체로는 의미가 없고 원소의 유무만 중요하기 때문이다.

예를 들어보자.

```coq
Inductive True := I.
Inductive False :=.
```

여기서 중요한 것은 True는 원소가 있어서 증명 가능하고, False는 원소가 없어서 증명 불가능하다는 것이다.
True가 100개의 원소를 가지든 1개의 원소를 가지든 상관없다.

## Predicate or Property

```coq
Inductive ev : nat -> Prop :=
  | ev_O : ev O
  | ev_SS n (EVN : ev n) : ev (S (S n)).
```

위 `ev`라는 type은 `?는 짝수`라는 자연수에 대한 predicate이다.
즉, 자연수 n이 주어졌을 때 `n은 짝수`라는 명제가 된다.
다른 관점에서 보면 `ev`는 `짝수`라는 자연수에 대한 성질이다.

`ev`의 원소들은 각각 특정 prop의 원소이다.
즉, 어떤 자연수에 대한 property의 증명이 된다.

* `ev_O`는 prop `ev O`의 원소이다. 즉, `ev O`이 증명 가능함 => 0은 짝수임을 뜻한다.
* `ev_SS n EVN`은 prop `ev (S (S n))`의 원소이다.
이 때 이 원소가 존재할 필요충분조건은 `ev n`의 원소 `EVN`이 존재하는 것이다.
따라서 `ev n`의 원소가 존재 <=> n이 짝수이면
`ev (S (S n))`의 원소가 존재 <=> S (S n)이 짝수.

각 원소가 대응하는 자연수는 같을 수도 있고 다를 수도 있다.

## Relation

```coq
Inductive le : nat -> nat -> Prop :=
  | le_n n : le n n
  | le_S n m (LE : le n m) : le n (S m).
```

위 `le`라는 type은 `?는 ?보다 작거나 같다`라는 두 자연수에 대한 relation이 된다.
즉, 두 자연수 n과 m이 주어졌을 때 `n은 m보다 작거나 같다`라는 명제가 된다.

`ev`와 마찬가지로 `le`의 원소들은 각각 특정 prop의 원소이다.
즉, 어떤 두 자연수에 대한 명제의 증명이 된다.

* `le_n n`은 prop `le n n`의 원소이다. 즉 n은 n보다 작거나 같음을 뜻한다.
* `le_S n m LE`는 `le n (S m)`의 원소이다.
즉 `LE : le n m`이 주어지면 <=> n이 m보다 작거나 같으면
`le n (S m)`의 원소가 존재 <=> n은 (S m)보다 작거나 같음을 뜻한다.

## Induction Technique on Inductive Type

귀납적으로 정의된 type의 induction principle이 원하는 모양이 아닐 때 쓸 수 있는 technique중 하나.
원래 명제를 강화시키는 방향으로 새로운 inductive type을 추가한 후 그놈을 이용해서 induction.
