# Tactics

사실 모든 증명은 `apply`(term 제시), `intro`(forall), `assert`(let-in),
`destruct`(match) 만으로 증명 가능하다.

## `with` Clause

`apply`같은 tactic에서 알 수 없는 unbounded variable이 나타났을 때,
binding을 explicit하게 제시해준다.
예를 들어 eq_trans를 적용할 때, x = y -> y = z -> x = z이므로 goal a = b를
두 개의 subgoal a = y와 y = b로 치환시키는데, 이 때 unbound variable y가 등장한다.
x와 z는 unification을 통해 각각 a, b가 binding되었다.

순서는 왼쪽부터.

## Intropattern

Intropattern은 local context에 추가될 variable이나 hypothesis 등의 이름을 줄 때 쓰인다.
주로 `intros` tactic에서 쓰이고 물론 다른 tactic에서도 variable이나 hypothesis를
추가할 때 쓰인다. 순서는 왼쪽부터.

### Naming Patterns

```coq

ident               (* Use the specified name *)
?                   (* Coq generates the fresh name *)
?ident              (* Generate the fresh name with prefix ident *)
_                   (* Discard the matched part *)
```

### Splitting Patterns

`[| n IHn]`처럼 주로 `induction`, `destruct`의 `as` clause에서 쓰인다.

### Equality Patterns

Introducing하는 hypothesis가 equality일 때 사용하는 pattern들.

```coq
->                  (* intro x. rewrite -> x. *)
<-                  (* intro x. rewrite <- x. *)
[= intropattern]    (* applies either injection or discriminate. *)
```

## `in` Clause

`in` clause는 goal 대신 hypothesis에 tactic을 적용시킨다.
Fundamental building block은 아니고 sugar.
`assert`로 표현 가능하기 때문이다.

대부분 가능하나 모든 tactic에서 가능한 것은 아니다.

## `at` Clause

Goal의 conclusion에서 몇 번째 자리(occurence)를 선택.

## `as` Clause

`as intropattern` clause는 tactic을 hypothesis에 적용시킨 결과를
새로운 이름과 함께 context에 추가한다.
`destruct`처럼 애초부터 hypothesis에 적용하는 tactic도 있는 반면,
`in` clause를 이용해서 hypothesis에 적용하는 tactic도 있다.

`as` clause 없이 사용하면 Coq이 알아서 이름을 붙이거나 기존 hypothesis를 대체한다.

대부분 가능하나 모든 tactic에서 가능한 것은 아니다.
특히, conversion tactic에서는 사용할 수 없다.

## Basic Tactics

* Applying Theorem
  * `apply`: apply theorem by backward reasoning
    * `with` clause: instantiate unbound variable
    * `in` clause: forward reasoning on hypothesis
  * `exact`, `refine`, `assumption` 등등
* Induction Theorem
  * `induction`: `apply <induction_principle>`
  * `destruct`: case analysis on constructor.
  * `constructor`: `apply <constructor>`
    * `split`: `constructor 1` for one-constructor type
    * `exists`: `constructor 1 with` for one-constructor type
    * `left` & `right`: `constructor 1 & 2` for two-constructor type
  * `inversion`: backtrack an inductive type by its constructor
* Managing Local Context
  * `intros`: introdue variable or hypothesis
  * `revert`: introduce한 것을 다시 premise로 내림
  * `rename`: rename hypothesis
  * `set`: alias를 만들어 local context에 추가하고 goal에서 해당 term을 replace
  * `assert`: add hypothesis to the local context, creating the subgoal
    * `:=`: provide proof term directly, so does not create subgoal
  * `specialize`: hypothesis의 premise에 값을 정해준다. (forward)
  * `generalize`: goal의 특정 subterm을 해당 type의 premise로 일반화한다. (backward)
    * `generalize dependent`: 관련 있는 hypothesis도 다 revert
* Equality 관련
  * `reflexivity`: `apply eq_refl`
  * `symmetry`: `apply eq_sym`. A = B를, B = A로.
  * `transivity`: `apply eq_trans with`. A = B를 A = C, C = B로.
  * `f_equql`: `apply f_equal`. f x = f y를 x = y로.
  * `rewrite`: `apply eq_ind_r` (->), `apply_eq_ind` (<-).
  * `discriminate`: false equality implies everything
  * `injection`: use the fact that constructor is injective
* Logic 관련
  * `absurd`: ~P와 P를 증명하면 False가 나오므로 match하면 모든 것을 증명 가능.
  * `contradiction`: automatic proof by contradiction.
* Conversion 관련
  * `cbv`: fine-grained control of conversion
  * `simpl`: 가능한 가장 간단한 형태로 reduction
  * `unfold`: delta reduction to the specified constant
* Existential 관련
  * `evar`: add evar
  * `instantiate`: instantiate evar
* Automation
  * `eauto`: apply를 자동화.
  * `lia`: linear arithmetic을 자동화.
  * `nia`: non-linear arithmetic도 자동화.

### Examples

```coq
apply P, Q in H. (* apply [term [with binding]],+ [in [ident],+ [as ident]] *)

induction n.
(* induction [term [as intropattern] [eqn:intropattern] [at clause]],+ [using induction_principle] *)
destruct n as [].
(* destruct [term [as intropattern] [eqn:intropattern] [at clause]],+ *)
constructor 1.
split. exists x. left. right.
inversion H.
(* inversion [term] [as ident] [in [ident],+] *)

intros n, m. (* intros [intropattern]* *)
revert n, m.
rename H1 into H2, H3 into H4.
set (sum := m + n) at 1.
assert (H : True). assert (H := I).
specialize H with (n := 1).
generalize (x + y + y).

reflexivity. symmetry. transivity B. f_eqaul. discriminate.
rewrite <- H. rewrite H.
injection H. (* injection H [as H1] *)

absurd P. contradiction.

cbv iota. cbv delta [plus].
simpl. unfold not.
```

## Applying Theorem

### `apply`: Backward Reasoning

Hypothesis중에 conclusion이 현재 goal type인 것을 apply해서 goal을 달성하고자 한다.
만약 hypothesis가 goal type이라면 exact와 동치이다.
만약 hypothesis가 goal type을 제외하고 n개의 premise를 가지면 n개의 subgoal이 생긴다.

```coq
(* Definition tactic : forall (A B : Prop) (x : A) (p : A -> A -> B), B := {goal}. *)
Goal forall (A B : Prop) (x : A) (p: A -> A -> B), B.
Proof.
  (* fun (A B : Prop) (x : A) (p : A -> A -> B) => {goal} *)
  intros A B x p.
  (* p {goal1 : A} {goal2 : A} *) apply p.
  (* (goal1) *) - (* x *) apply x.
  (* (goal2) *) - (* x *) apply x.
  Qed.

(* Definition tactic : forall (A B : Prop) (x : A) (p : A -> A -> B), A -> B := {goal}. *)
Goal forall (A B : Prop) (x : A) (p: A -> A -> B), A -> B.
Proof.
  (* fun (A B : Prop) (x : A) (p : A -> A -> B) => {goal} *)
  intros A B x p.
  (* p {goal : A} *) apply p.
  (* x *) apply x.
  Qed.
```

### `apply in`: Forward Reasoning

Goal에 apply하는 것이 아닌 다른 hypothesis에 apply하는 것.
Goal에 apply할 때는 backward reasoning, 즉 `B`를 `(A -> B) A`로 바꾼다면,
Hypothesis에 apply할 때는 forward reasoning, 즉 `A`를 `(A -> B) A`로 바꾼다.
as clause를 쓰면 forward reasoning의 결과에 name을 붙여서 새 hypothesis로 만들 수 있다.

```coq
(* Definition tactic : forall (A B : Prop) (a : A) (p : A -> B) => {goal}. *)
Goal forall (A B : Prop) (a : A) (p : A -> B), B.
Proof.
  (* fun (A B : Prop) (a : A) (p : A -> B) => {goal} *)
  intros A B a p.
  (* let b : B := p a in {goal} *)
  apply p in a as b.
  (* b *) exact b.
  Qed.
```

### `apply with`

대부분 hypothesis를 apply할 때 dependent premise term은 implicit하더라도 유추 가능하다.
하지만 그렇지 않은 경우 `with` clause로 explicit하게 알려줘야 한다.

Hypothesis나 local context에서 term이나 variable을 찾아서 줄 수 있고,
혹은 `(p := term)` 처럼 직접 named value를 줄 수 있다.

```coq
(* Definition tactic : (forall B C : Prop, B -> C) -> (forall A : Prop, A) := {goal}. *)
Goal (forall B C : Prop, B -> C) -> (forall A : Prop, A).
Proof.
  (* fun (contra : (forall B C : Prop, B -> C)) (A : Prop) => {goal} *)
  intros contra A.
  (* contra True A {goal: True} *) apply contra with True.
  (* I *) exact I.
  Qed.
```

이는 existential variable과 관련이 있다.
`forall {B C : Prop}, B -> C`는 `?B -> ?C where [?B |- Prop], [?C |- Prop]`인데,
이를 `A`에 apply하면 existential variable `?C`는 A로 추론이 되지만, B는 그렇지 않다.
따라서 existential variable `?B`를 명시적으로 알려주어야 한다.
위 예시에서는 `True`로 instantiate했다.

## Induction Theorem

### `induction`

Induction principle을 이용하여 각 constructor에 대한 subgoal을 만든다.
`apply <induction principle>`과 같다.
`using` clause를 이용하면 induction principle을 명시적으로 줄 수 있다.

또한 `constructor`처럼 `intros`는 자동이다.

n + O = O을 예시로 들어보자. nat에 대한 induction을 진행한다.

* `nat_ind`의 type은
`forall P : nat -> Prop, P O -> (forall n, P n -> P (S n)) -> (forall n, P n)`.
* 자명하게도 P는 `fun n => n + O = O`이다.
* 따라서 apply할 때 `P O`, 즉 `O + O = O`의 원소가 필요하고,
* `forall n, P n -> P (S n)`,
즉 `forall n, (n + O = n) -> (S n + O = S n)`의 원소도 필요하다.
이 원소는 `fun (n' : nat) (IHn' : n' + O = n') => {term: S n' + O = S n'}`이다.
즉 `n' : nat`과 `IHn' : n' + O = n'`을 추가로 가지고 `S n' + O = S n'`를 보여야 한다.

```coq
(* Definition tactic : forall n, n + O = n := {goal}. *)
Goal forall n, n + O = n.
Proof.
  (* fun n : nat => *) intros n.
  (* nat_ind (fun n0 => n0 + O = n0)
      {goal1: O + O = O}
      (fun (n' : nat) (IHn' : n' + O = n') => {goal2 : S n' + O = S n'}) n
  *)
  induction n as [|n' IHn'].
  (* goal1 *) - (* eq_refl *) reflexivity.
  (* goal2 *) - simpl.
    (* eq_ind_r (fun n0 : nat => S n0 = S n') eq_refl IHn' *)
    rewrite -> IHn'. reflexivity.
  Qed.
```

### `destruct`

Inductive type hypothesis에 대한 case analysis다.
`induction`과 다른 점은 local context에 induction hypothesis가 없다는 것이다.
따라서 `induction`과 마찬가지로 `apply <induction principle>`로 나타낼 수 있다.
`using` clause를 이용하면 induction principle을 명시적으로 줄 수 있다.

또한 destruct는 match로 더 깔끔하게 표현할 수 있다.

```coq
(* Definition tactic : forall n, match n with O =>
                                 | n = O
                                 | _ => exists n', n = S n'
                                 end := {goal}. *)
Goal forall n, match n with O => n = O | _ => exists n', n = S n' end.
Proof.
  (* fun n => match n with O => {goal1} | S n' => {goal2} end *)
  destruct n as [| n'].
  (* goal1 *) - (* eq_refl *) reflexivity.
  (* goal2 *) - (* ex_intro (fun n => S n' = S n) n' eq_refl *) exists n'. reflexivity.
  Qed.
```

### `constructor`

`constructor n`은 `apply <nth constructor>`와 동치.
한편, constructor는 apply보다 조금 더 유용해서 필요한 만큼 intros를 자동으로 해준다.

```coq
(* Definition tactic : forall A : Prop, True := {goal}. *)
Goal forall A : Prop, True.
Proof.
  (* fun _ : Prop => I *) constructor 1.
  Qed.
```

### `split`

Constructor가 하나뿐인 inductive type에서 사용 가능하고, `constructor 1`과 동치.
Conventionally `A /\ B /\ C`를 split한다 해서 이름지어짐.

```coq
(* Definition tactic : forall (A B : Prop), A -> B -> and A B := {goal}. *)
Goal forall A B : Prop, A -> B -> and A B.
Proof.
  (* fun (A B : Prop) (a : A) (b : B) => {goal} *)
  intros A B a b.
  (* conj {goal1 : A} {goal2 : B} *) split.
  (* goal1 *) - (* a *) apply a.
  (* goal2 *) - (* b *) apply b.
  Qed.
```

### `exists`

Constructor가 하나뿐인 inductive type에서 사용 가능하고, `constructor 1 with`과 동치.
Conventionally `exists x, P x`에서 x를 제시한다 해서 이름지어짐.

```coq
(* Definition tactic : exists n, n = 5 := {goal}. *)
Goal exists n, n = 5.
Proof.
  (* ex_intro (fun n => n = 5) 5 {goal: 5 = 5} *) exists 5.
  (* eq_refl *) reflexivity.
  Qed.
```

### `left` & `right`

Constructor가 두 개인 inductive type에서 사용 가능하고,
각각 `constructor 1`, `constructor 2`와 동치.
Conventionally `A \/ B`에서 A 또는 B 하나만 제시한다 해서 이름지어짐.

```coq
(* Definition tactic : or (b = true) (b = false) := {goal}. *)
Goal forall b, or (b = true) (b = false).
Proof.
  (* if b then {goal1} else {goal2} *)
  destruct b.
  (* goal1 *) - (* or_introl eq_refl *) left. reflexivity.
  (* goal2 *) - (* or_intror eq_refl *) right. reflexivity.
  Qed.
```

### `inversion`

inductive type을 만들 수 있는 방법은 constructor밖에 없음을 이용한다.
annotated inductive type일 때 쓸 수 있다.
어떤 hypothesis가 inductive type이라면 가능한 constructor만을 골라서 역으로 reasoning.

## Managing Local Context

### `intro(s)`

Implication `A -> B`, 혹은 더욱 일반적으로 `forall x : A, B`를 증명할 때,
$x \in A$ 를 가정하고 B를 증명한다.
즉, `x : A`를 local context에 추가하고, goal은 `B`가 된다.

`intro` tactic은 말 그대로 goal의 premise를 local context에 introduce하는 tactic이다.
`let x := c : T in term`처럼 애초에 local context인 것도 introduce한다.
만약 introduce할 수 없다면 hnf tactic을 apply하여 head reduction을 진행한다.
그럼에도 불구하고 introduce할 수 없다면 tactic이 실패한다.

만약 identifier를 명시하지 않는다면 coq이 알아서 fresh name을 만들어 쓴다.
하지만 왠만하면 써주자.

`intros`는 `intro`의 plural form. intro pattern과 함깨 사용한다.
Intro pattern이 주어지지 않으면, head constant를 만날 때까지 introduce한다.
이게 0개일 수도 있다. 즉, 절대 실패하지 않는다.

그게 아니라면 intro pattern에 따라 tactic의 결과가 달라진다.

### `revert`

Hypothesis를 goal로 내린다. `intro`의 역함수.

```coq
(* Definition tactic : forall (A : Prop) (x : A), A := {goal}. *)
Goal forall (A : Prop) (x : A), A.
Proof.
  (* fun A => {goal} *) intros A.
  (* {goal: forall (A : Prop) (x : A), A} A *) revert A.
  (* fun A x => {goal} *) intros.
  (* x *) apply x.
  Qed.
```

### `rename`

Hypothesis를 rename.

```coq
(* Definition tactic := forall (A : Prop) (x : A), A := {goal}. *)
Goal forall (A : Prop) (x : A), A.
Proof.
  (* fun A => {goal} *) intros A.
  (* let B := A in {goal} *) rename A into B.
  (* fun x => {goal} *) intros x.
  (* x *) exact x.
Qed.
```

### `remember`

특정 term을 local context에 추가하고 추가한 variable로 해당 term을 rewrite.

```coq
(* Definition tactic : forall x, S x = S x := {goal}. *)
Goal forall (x y : nat), S x = S y.
Proof.
  (* fun x => {goal} *) intros x.
  (* let sx := S x in Heqsx : sx = S x := eq_refl in {goal: sx = sx} *)
  remember (S x) as sx.
  (* eq_refl *) reflexivity.
Qed.
```

### `subst`

`rewrite in *` 자동화. `x = e`나 `e = x` assumption을 rewrite 후 clear.

```coq
Goal forall x y, x = y -> y = 3 -> x = 3.
Proof.
  intros x y EQ H.
  (* subst *)
  subst y.
  assumption.
Qed.
```

### `set`

alias를 만들어 local context에 추가하고, goal에서 해당 term을 replace.

```coq
Goal forall x y, x + y = O -> x + y + x + y = x + y.
Proof.
  intros x y H.
  set (xy := x + y) at 2.
  rewrite H.
  unfold xy.
  reflexivity.
Qed.
```

### `assert`

Adds a new hypothesis to the current subgoal and a new subgoal before it to prove the hypothesis.

즉, 새로운 hypothesis를 먼저 증명한 후 현 goal을 증명할 때 쓰겠다는 뜻.

만약 `assert (ident : term)`이 아닌 `assert (ident := term)`으로 쓴다면,
subgoal을 추가함과 동시에 증명하는 것이므로,
새 hypothesis를 현재 subgoal에 추가하기만 한다.

```coq
(* Definition tactic : forall (A : Prop), True \/ A := {goal}. *)
Goal forall (A : Prop), True \/ A.
Proof.
  (* fun A : Prop => {goal} *) intros A.
  (* let H : forall (P Q : Prop), P -> P \/ Q := {goal1} in {goal2: True \/ A} *)
  assert (H : forall (P Q : Prop), P -> P \/ Q).
  (* goal1 *) - (* fun (P Q : Prop) (p : P) => {goal} *) intros P Q p.
    (* or_introl {goal: P} *) constructor 1.
    (* p *) exact p.
  (* goal2 *) - (* H True A {goal: True} *) apply H.
    (* I *) exact I.
  Qed.

(* Definition tactic : forall (A : Prop), A \/ True := {goal}. *)
Goal forall (A : Prop), A \/ True.
Proof.
  (* fun A : Prop => {goal} *) intros A.
  (* let H : forall P Q : Prop, Q -> P \/ Q := fun (P Q : Prop) (q : Q) => @or_intror P _ q in {goal} *)
  assert (H := fun (P Q : Prop) (q : Q) => @or_intror P _ q).
  (* H A True {goal: True} *) apply H.
  (* I *) exact I.
  Qed.
```

### `specialize`

Hypothesis의 premise에 특정 값을 준다. Generalize의 반대.

```coq
(* Definition tactic : (forall (x y : nat), x = y) -> 3 = 7 := {goal}. *)
Goal (forall (x y : nat), x = y) -> 3 = 7.
Proof.
  (* fun H => {goal} *) intros H.
  (* let H := H 3 7 in {goal} *) specialize H with 3 7.
  (* H *) apply H.
Qed.
```

### `generalize`

`T1`, `T2`, ... type (sub)term들을 generalized term x1, x2, ...로 치환하고,
goal에 `x1 : T1`, `x2 : T2`, ... premise를 추가.
즉, 기존 proposition보다 더욱 general한 proposition을 증명하겠다는 것.

It is nothing more than assert then apply.

```coq
(* Definition tactic : forall (x : nat) (P : nat -> Prop) (p : P (S x)), P (S x) := {goal}. *)
Goal forall (x : nat) (P : nat -> Prop) (p : P (S x)), P (S x).
Proof.
  (* fun (x : nat) (P : nat -> Prop) => {goal} *) intros x P.
  (* {goal: forall n : nat, P n -> P n} (S x) *) generalize (S x).
  (* fun (n : nat) (p : P n) => {goal} *) intros n p.
  (* p *) exact p.
  Qed.
```

### `generalize dependent`

주어진 (sub)term으로 goal 뿐만 아니라 dependent한 hypotheses도 generalize.
한편, generalization 후 generalization에 해당하는 premise가 goal에 추가되기 때문에,
generalized hypothesis도 local context에서 goal의 premise로 내려와야 함.

다음 예시를 위의 `generalize` 예시와 비교하며 읽어보자.

```coq
(* Definition tactic : forall (x : nat) (P : nat -> Prop) (p : P (S x)), P (S x) := {goal}. *)
Goal forall (x : nat) (P : nat -> Prop) (p : P (S x)), P (S x).
Proof.
  (* fun (x : nat) (P : nat -> Prop) (p : P (S x)) => {goal} *) intros x P p.
  (* {goal : forall n : nat, P n -> P n} (S x) p *) generalize dependent (S x).
  (* fun (n : nat) (p : P n) => {goal} *) intros n p.
  (* p *) exact p.
  Qed.
```

## Equality 관련

### `reflexivity`

`Coq.Init.Logic.eq_refl`을 통한 equality의 직접 증명이다.

Nothing more than `apply eq_refl`과 같다.

```coq
(* Definition tactic : forall (A : Type) (x : A), x = x := {goal}. *)
Goal forall (A : Type) (x : A), x = x.
Proof.
  (* fun (A : Type) (x : A) => {goal} *) intros A x.
  (* eq_refl *) reflexivity.
  Qed.
```

### `symmetry`

`{a} = {b}` 형태의 goal을 `{b} = {a}`로 바꾼다.
이는 `{b} = {a}`를 주면 `{a} = {b}`를 돌려주는 함수를 apply하는 것과 같다.
그리고 이에 해당하는 constant `eq_sym`이 이미 있다.

Nothing more than `apply eq_sym`.

```coq
Definition eq_sym (A : Type) (x y : A) (x0 : x = y) : y = x :=
  match x0 in (_ = H) return (H = x) with
  | eq_refl => eq_refl
  end.

(* Definition tactic : forall (A : Type) (x : A) *)
Goal forall (A : Type) (x y : A), x = y -> y = x.
Proof.
  (* fun (A : Type) (x y : A) (H : x = y) => *) intros A x y H.
  (* eq_sym {goal : x = y} *) symmetry.
  (* H *) exact H.
  Qed.
```

### `transitivity`

`{a} = {b}` 형태의 goal을 `{a} = {c}`와 `{c} = {b}` 두 개의 subgoal로 바꾼다.
이는 `{a} = {c}`, `{c} = {b}`를 주면 `{a} = {b}`를 돌려주는 함수를 apply하는 것과 같다.
그리고 이에 해당하는 constant `eq_trans`가 이미 있다.

Nothing more than `apply eq_trans with ?`.

```coq
Definition eq_trans (A : Type) (x y z : A) : (x = y) -> (y = z) -> (x = z) :=
  fun (H1 : x = y) (H2 : y = z) =>
    match H2 in (_ = z) return (x = z) with
    | eq_refl => H1
    end.

(* Definition tactic : forall (A : Type) (x y z : A) : (x = y) -> (y = z) -> (x = z) := {goal}. *)
Goal forall (A : Type) (x y z : A), (x = y) -> (y = z) -> (x = z).
Proof.
  (* fun (A : Type) (x y z : A) (H1 : x = y) (H2 : y = z) => {goal} *)
  intros A x y z H1 H2.
  (* eq_trans {goal1 : x = y} {goal2 : y = z} *)
  transitivity y.
  (* goal1 *) - (* H1 *) exact H1.
  (* goal2 *) - (* H2 *) exact H2.
  Qed.
```

### `f_equal`

`f_equal : fun [A B : Type] (f : A -> B) [x y : A] x = y -> f x = f y`
를 이용하여 `f x = f y`를 `x y`로 치환한다.

### `rewrite`

`H : {a} = {b}`라는 hypothesis가 local context에 있을 때,
`rewrite <- H.`는 `{b}`를 `{a}`로, `rewrite -> H.`는 `{a}`를 `{b}`로 바꾼다.

`rewrite` tactic은 `eq`에서 파생된 `eq_ind`와 `eq_ind_r` 함수를 사용한다.
정의는 다음과 같다.

* eq_ind : "P x가 참이고 임의의 y에 대하여 x = y이면 P y가 참이다"
따라서 `p : P x`만 있으면 `H : x = y`로 `eq_ind _ x P p y H : P y`를 만들 수 있다.
따라서 `P y`는 `P x`로 바꿀 수 있다.
(`rewrite <- (x = y)`, backward reasoning)
* "P y가 참이고 임의의 x에 대하여 x = y이면 P x가 참이다"
따라서 `p : P y`만 있으면 `H : x = y`로 `eq_ind_r _ y P p x H : P x`를 만들 수 있다.
다라서 `P y`는 `P x`로 바꿀 수 있다.
(`rewrite -> (x = y)`, backward reasoning)

```coq
Definition eq_ind (A : Type) (x : A) (P : A -> Prop) : P x -> forall y : A, x = y -> P y :=
  fun (p : P x) (y : A) (H : x = y) =>
    match H in (_ = a) return (P a) with
    eq_refl => p
    end.

Definition eq_ind_r (A : Type) (y : A) (P : A -> Prop) : P y -> forall x : A, x = y -> P x :=
  fun (p : P y) (x : A) (H : x = y) =>
    match (eq_sym H) in (_ = a) return (P a) with
    eq_refl => p
    end.

(* Definition tactic : forall (x y : nat) , x = y -> S x = S y := {goal}. *)
Goal forall (x y : nat), x = y -> S x = S y.
Proof.
  (* fun (x y : nat) (H : x = y) => {goal} *)
  intros x y H.
  (* eq_ind _ x (fun n : nat => S x = S n) {goal} y H *)
  rewrite <- H.
  (* eq_refl *)
  reflexivity.
  Qed.

(* Definition tactic : forall (x y : nat), x = y -> S x = S y := {goal}. *)
Goal forall (x y : nat), x = y -> S x = S y.
Proof.
  (* fun (x y : nat) (H : x = y) => {goal} *)
  intros x y H.
  (* eq_ind_r _ _ (fun n : nat => S n = S y) {goal} _ H *)
  rewrite -> H.
  (* eq_refl *)
  reflexivity.
  Qed.
```

### `discriminate`

`(2 = 3) -> False`처럼 false equality가 hypothesis로 있는 경우
`absurd`처럼 모든 goal을 증명할 수 있다.

```coq
(* Definition tactic : forall A : Prop, 0 = 1 -> A := {goal}. *)
Goal forall A : Prop, 0 = 1 -> A.
Proof.
  (* fun (A : Prop) (H : 0 = 1) => {goal} *)
  intros A H.
  (* match H in (_ = y) return match y with O => True | _ => A end with eq_refl => I end *)
  discriminate H.
  Qed.
```

### `injection`

모든 constructor가 injective function임을 이용한다.
어떤 hypothesis가 `term1 = term2` 형태인데 두 term이 inductive type이면,
constructor의 argument가 같다는 premise로 바꿀 수 있다.
`as` clause를 이용하면 premise가 아니라 hypothesis로 local context에 추가된다.

## Logic 관련

### `absurd`

`absurd`는 false elimination이다.
`(A /\ ~A) -> False`와 `forall C : Prop, False -> C`를 활용하는 것.
현재 goal이 뭐든간에 `A`와 `~A`를 증명한다면(absurd) 이에 따라 False를 증명할 수 있고,
따라서 임의의 명제 C를 증명할 수 있다.

참고로 `~A`는 Coq.Init.Logic.not이다.

Nothing more than `apply y in x. destruct x.`, where `x : A, y : ~A`.

```coq
(* Definition tactic : forall (A B : Prop) (x : A) (y : ~A), B := {goal}. *)
Goal forall (A B : Prop) (x : A) (y : ~A), B.
Proof.
  (* fun (A B : Prop) (x : A) (y : ~A) => {goal} *)
  intros A B x y.
  (* match {goal1: ~A} {goal2: A} return B with end *)
  absurd A.
  (* goal1 *) - (* y *) exact y.
  (* goal2 *) - (* x *) exact x.
  Qed.
```

### `contradiction`

Proof by contradiction, 즉 귀류법이다. `absurd`와 비슷한데, 좀 더 범용적이다.

`contradiction H.`는

1. H가 False처럼 empty inductive type인 경우 proof by contradiction, q.e.d.
즉, 임의의 type A에 대하여 `forall x : H, A`가 성립한다. (자명)
Definition 관점에서 보면 `match x return goal_type with end`다.
2. H가 ~P인 경우 goal을 P로 바꾼다. False elimination을 하기 위해서이다.
또한, 만약 P가 hypothesis에 있다면 `exact`로 바로 증명을 끝낸다.
Definition 관점에서 보면 `match H {new_goal} return goal_type with end`다.

참고로 여기서 not은 `Coq.Init.Logic.v`에 정의된 constant not이다.

만약 H 없이 `contradiction`만 쓴다면 일단 `intros`를 한 후,
`assumption`처럼 hypothesis중에 condtradiction으로 증명을 바로 끝낼 수 있는 걸 찾는다.
만약 못찾으면 실패한다.
(2번을 적용하려면 ~P와 P가 동시에 hypothesis에 있어야 한다)

```coq
Goal forall (A : Prop), False -> A.
Proof.
  intros A f.
  (* contradiction f. *)
  contradiction.
  Qed.

Goal forall (A B : Prop) (a : A) (a' : ~A), B.
Proof.
  intros A B a a'.
  (* contradiction a'. *)
  contradiction.
  Qed.
```

## conversion 관련

### `cbv`

특정 reduction(beta, delta, zeta, iota)을 수행한다.

* beta reduction: resolve fun.
  * `(fun x => S x) (S O)` into `(S O)`
* delta reduction: unfold **transparent** constant to its definition.
  * `not P` into `P -> False`
  * `[]`를 이용해서 특정 constant들만 unfold할 수 있다.
* zeta reduction: resolve let-in definition.
  * `let x := O in S x` into `S O`
* iota reduction: resolve match expression and unfold fix.
  * `match S O in O => False | _ => True` into `True`  
  * `fix plus n m := match n with O => m | S n' => S (plus n' m) end` into
  `fix plus n m :=
    match n with
    | O => m
    | S n' =>
      S
        ((fix plus n0 m :=
            match n0 with
            | O => m
            | S n1 => S (plus n1 m)
            end) n' m)
    end`

```coq
Definition foo (A B : Prop) := fun n => match (let x := n in x) with O => A | _ => B end.
Definition bar (P : Prop) := P -> False.

Goal forall (A : Prop) (n : nat), bar (foo (bar True) (bar False) (S n)) -> A.
Proof.
  (* F : bar (foo (bar True) (bar False) (S n)) *)
  intros A n F.
  (* F : (fun P => p -> False)
          (foo ((fun P => P -> False) True) ((fun P => P -> False) False) (S n)) *)
  cbv delta [bar] in F.
  (* F : foo (True -> False) (False -> False) (S n) -> False *)
  cbv beta in F.
  (* F : (fun A B n => match (let x := n in x) with
                       | O => A
                       | _ => B
                       end) (True -> False) (False -> False)
          (S n) -> False *)
  cbv delta in F.
  (* F : match (let x := S n in x) with 
         | O => True -> False
         | _ => False -> False
         end -> False *)
  cbv beta in F.
  (* F : match S n with
         | O => True -> False
         | _ => False -> False
         end -> False *)
  cbv zeta in F.
  (* F : (False -> False) -> False *)
  cbv iota in F.
  (* let f : False := F {goal: False -> False} in match f return A with end *)
  destruct F.
  (* fun K : False => K *)
  intros K. apply K.
  Qed.
```

### `simpl`

delta, beta, zeta, iota reduction을 smart하게 잘 수행하여 가장 간단한 형태로 나타낸다.

* beta, zeta, iota reduction은 가능하면 수행한다.
* delta reduction은 수행 후 iota reduction이 가능한 경우
즉 definition이 fix나 match를 포함하고 있는 경우에만 수행한다.
아래 예시에서 simpl이 foo는 delta-reduce하고 bar는 그렇지 않다.

```coq
Definition foo (A B : Prop) := fun n => match (let x := n in x) with O => A | _ => B end.
Definition bar (P : Prop) := P -> False.

Goal forall (A : Prop) (n : nat), bar (foo (bar True) (bar False) (S n)) -> A.
Proof.
  (* F : bar (foo (bar True) (bar False) (S n)) *)
  intros A n F.
  (* F : bar (bar False) *)
  simpl in F.
  (* F : (fun P => P -> False) ((fun P => P -> False) False) *)
  cbv delta in F.
  (* F : (False -> False) -> False *)
  simpl in F.
  (* let f : False := F {goal: False -> False} in match f return A with end *)
  destruct F.
  (* fun K : False => K *)
  intros K. apply K.
  Qed.
```

### `unfold`

특정 constant에 대해 delta reduction을 수행한 후,
가능한 만큼 beta, zeta, iota reduction을 수행한다.

```coq
Definition foo (A B : Prop) := fun n => match (let x := n in x) with O => A | _ => B end.
Definition bar (P : Prop) := P -> False.

Goal forall (A : Prop) (n : nat), bar (foo (bar True) (bar False) (S n)) -> A.
Proof.
  (* F : bar (foo (bar True) (bar False) (S n)) *)
  intros A n F.
  (* F : foo (True -> False) (False -> False) (S n) -> False *)
  unfold bar in F.
  (* F : (False -> False) -> False *)
  unfold foo in F.
  (* let f : False := F {goal: False -> False} in match f return A with end *)
  destruct F.
  (* fun K : False => K *)
  intros K. apply K.
  Qed.
```

## Existential 관련

### `evar`

`assert`와 비슷하지만 hypothesis를 추가하는 것이 아닌 existential variable을 추가한다.
해당 existential variable은 나중에 resolve되어야 하고, 결론적으로 보면 `assert`와 동치이다.

```coq
(* Definition tactic : True := {goal}. *)
Goal True.
Proof.
  (* let x : True := {?x: True(shelved)} in {goal} *) evar (x : True).
  (* x *) apply x.
  Unshelve.
  (* I *) apply I.
  Qed.
```

### `instantiate`

existential variable을 instantiate한다. 숫자를 쓸 경우 오른쪽부터 1이다.

* `in |- *` (default) : goal에 적용
* `in H` : hypothesis H에 적용
* `in (value of x)` : local definition x의 value에 적용
* `in (type of x)` : local definition x의 type에 적용

```coq
(* Definition tactic : True := {goal}. *)
Goal True.
Proof.
  (* let x : True := {?x : True(shelved)} in {goal} *) evar (x : True).
  (* I *) instantiate (1 := I) in (value of x).
  (* x *) apply x.
  Qed.
```

## Automation

### `eauto`

apply (including reflexivity, rewrite, ...)를 자동화.

### `lia` & `nia`

Arithmetic을 자동화.
