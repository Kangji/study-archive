# Existential Variables

Coq에서는 unknown subterm을 existential variable(줄여서 evar)로 표현할 수 있다.
하지만 결국 unknown subterm은 받아들여지지 않기 때문에 실제 term으로 채워져야 한다.

```coq
_ (* hole *)
?ident
?[ident]
?[?ident]
```

많은 경우 evar은 inferrable해서 자동으로 채워진다.

## Check / Compute

Check나 compute의 결과에서 unsolved implicit arguments나 `_`를 evar로 표현한다.
이 때 `?ident`로 표현된다.

```coq
Inductive list (A : Type) :=
| nil
| cons (x : A) (tl : list A).

Inductive list' {A : Type} :=
| nil'
| cons' (x : A) (tl : list').
```

`Check cons`, `Check cons'`를 해보면:

```coq
cons
      : forall A : Type, A -> list A -> list A
cons'
      : ?A -> list' -> list'
where
?A : [ |- Type]
```

where clause는 evar `?A`가 특정 조건에서 특정 type이어야 함을 뜻한다.
지금 상황에서 특정 조건은 없고, 만족해야 하는 type은 `Type`이다.

## `e*` Tactics

A number of tactics have companion tactics that create existential variable,
when the base tactic would fail because of uninstantiated variables.

For example,

```coq
Example eapply_tactic: forall (H : forall n m p : nat, n < m -> m < p -> n < p) (x y : nat), x < y.
Proof.
  intros.
  eapply H.
```

In this case, apply tactic will fail since m is uninstantiated.
However, eapply tactic will create existential variable `?m` for m.

또한 shelved goal로 `?m : nat`이 생긴다.

`e*` tactic으로 생긴 evar들 및 shelved goal들은 마찬가지로 증명 과정에서 대부분 추론된다.
