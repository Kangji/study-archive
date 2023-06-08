# Inductive Types

`Inductive` command로 inductive type과 constructor들을 정의할 수 있다.

## Constructor

Constructor는 inductive type의 element를 귀납적으로 정의한다.

Constructor는 어떤 type이든 상관 없지만 반드시 해당 inductive type으로 끝나야 한다.
따라서 Constructor는 inductive type의 element일 수도 있고,
inductive type으로 끝나는 function type일 수도 있다.
중요한 것은 해당 inductive type으로 끝난다는 점이다.

## Universe

정의한 inductive type이 어떤 type universe에 속할지는 명시하지 않은 경우,
Coq이 가능한 가장 작은 universe로 유추한다.

Set과 Prop이 모두 가능한 상황에서는 Set으로 유추한다.

## Enumerated Type

가장 간단한 형태의 non-inductive type으로, 모든 constructor가 element인 경우이다.
Constructor가 없을 수도 있다.

Enumerated type은 사실 귀납적으로 정의되진 않았다.
그래서 애초에 `Variant`라는 command가 따로 있다.
하지만 `Variant`의 모든 기능을 `Inductive`로도 할 수 있기 때문에 알 필요 없다.

```coq
Inductive bool : Set :=
| true
| false.

Inductive True : Prop :=
| I.

Inductive False : Prop :=.
```

## Simple Inductive Type

가장 간단한 형태의 inductive type으로, `Set`, `Prop`등 가장 간단한 형태의 sort에 속한다.
이 경우 constructor는 argument type만 알려줘도 정의가 된다.

```coq
Inductive nat : Set :=
| O : nat
| S : nat -> nat.
```

이 때 constructor O는 그 자체로 nat의 원소이고,
constructor S는 n이 nat의 원소일 때 또 다른 nat의 원소 S n을 만든다.
그 결과 nat의 원소들은 귀납적으로 `O`(0), `S O`(1), `S (S O)`(2), ... 이렇게 된다.

```coq
Inductive binary : Set :=
| Z : binary (* one *)
| B0 : binary -> binary (* 2 * binary)
| B1 : binary -> binary (* 2 * binary + 1).
```

이 때 constructor Z는 그 자체로 binary의 원소이고,
constructor B0는 n이 binary의 원소일 때 또 다른 binary의 원소 B0 n을 만든다.
constructor B1은 n이 binary의 원소일 때 또 다른 binary의 원소 B1 n을 만든다.
그 결과 binary의 원소들은 귀납적으로 `Z`(1), `BO Z`(10), `B1 Z`(11), `BO BO Z`(100), `B1 BO Z`(101), ... 이렇게 된다.

```coq
Inductive nat_tree : Set :=
| Leaf : nat -> tree
| Parent : nat -> tree -> tree -> tree.
```

이 때 constructor Leaf는 nat n을 받아서 tree의 원소 Leaf n을 만들고,
constructor Parent는 nat n과 tree left와 right을 받아서 또 다른 tree의 원소 Parent n left right을 만든다.
그 결과 tree의 원소들은 귀납적으로 `Leaf n0`, `Parent n0 (Leaf n1) (Leaf n2)`, `Parent n0 (Parent n1 (Leaf n2) (Leaf n3)) (Leaf n4)`, ... 이렇게 된다.

```coq
Inductive empty : Set :=
| null : empty -> empty.
```

이 때 constructor null은 empty의 원소 e를 받아서 또 다른 empty의 원소 null e를 만드는데,
해당 집합이 공집합임은 쉽게 증명할 수 있다.

## Simple Annotated Inductive Type

이제 좀 머리가 아파진다. 왜냐하면 annotated inductive type들은 더이상 간단한 sort에 속하지 않기 때문이다.
이제 inductive type의 type은 **arity**라고 하는, conclusion이 simple sort인 type이다.
conclusion을 제외한 부분을 annotation이라고 한다.
(For example, `nat -> Prop`, `nat` -> `nat` -> `Set`)

```coq
Inductive even : nat -> Prop :=
| even_0 : even O
| even_SS : forall n : nat, even n -> even (S (S n)).
```

### Mathematical Approach

의미를 살펴보면, 모든 nat n에 대하여 Prop type인 `even n` type을 귀납적으로 정의하는 것이다.
이 type의 constructor들은 귀납적으로 특정 n에 대하여 `even n` type을 construct한다.
즉, constructor마다 construct하는 type이 다를 수 있다.
이 때 다른 부분은 annotation 부분이다.

위 예시에서 `even_0`는 `even_0`라는 `even O`의 원소를 construct한다.
반면 `even_SS`는 nat의 원소 n과 `even n`의 원소(let's say, p)가 주어졌을 때,
`even_SS n p`라는 `even (S (S n))`의 원소를 construct한다.

결론적으로, `even`이라는 annotated inductive type을 정의한 결과,
`even_0 : even O`,`even_SS 0 even_0 : even 2`, `even_SS 2 (even_SS 0 even_0) : even 4` 등 모든 짝수 n에 대하여는 Prop `even n`의 원소가 정의되었다.

### Ideation: n by m Boolean Matrix

역으로 해석이 아닌 정의를 해보자.
우리가 귀납적으로 정의할 `matrix`는 행렬의 행 수 `n : nat과` 열 수 `m : nat`이 필요하다.
즉 우리는 `forall n m : nat, matrix n m`라는 inductive type을 정의해야 한다.
우선, 1 by 1 matrix가 필요하다. 그러니 `scalar`라는 `matrix 1 1`의 constructor가 필요하겠다.
따라서 `scalar` constructor는 `bool -> matrix 1 1` type의 함수이다.
(행렬의 항 값도 필요해서 constructor가 bool을 받는다)

그 다음은 n by 1 matrix가 필요하다. 그러니 `vector`라는 `matrix n 1`의 constructor를 만들겠다.
그런데, 이는 귀납적으로 정의할 수 있다. 앞서 even에서 봤던 것 처럼,
`matrix n 1`과 bool 값을 가지고 `matrix (S n) 1`의 원소를 만들 수 있다.
따라서 `vector` constructor는 `forall n : nat, matrix n 1 -> bool -> matrix (S n) 1` type의 함수가 되겠다.
사실 `forall n : nat, matrix 1 1 -> matrix n 1 -> matrix (S n) 1`이거나,
`matrix 1 1 -> (forall n : nat, matrix n 1) -> matrix (S n) 1`이거나, 별 상관은 없다.
단지 constructor의 argument 문제이다.

마지막으로 n by m matrix가 필요하다. 그러니 `mat`라는 `matrix n m`의 constructor를 만들겠다.
`vector`와 마찬가지로 귀납적으로 정의해보자.
`matrix n m`과 `matrix n 1`을 가지고 `matrix n (S m)`의 원소를 만들 수 있다.
따라서 `mat`는 `forall n m: nat, matrix n m -> matrix n 1 -> matrix n (S m)` type의 함수가 되겠다. 마찬가지로 argument 순서는 별 상관이 없다.

```coq
Inductive bool_matrix: nat -> nat -> Set :=
| scalar : bool -> matrix 1 1
| vector : forall n : nat, matrix n 1 -> matrix 1 1 -> matrix (S n) 1
| mat: forall n m : nat, matrix n m -> matrix n 1 -> matrix n (S m).
```

잘 정의되었는지 살펴보자.

* `[false]` => scalar false
* `[true]` => scalar true
* `[false; false]` => vector 1 (scalar false) (scalar false)
* `[true; false; true]` => vector 2 (vector 1 (scalar true) (scalar false)) (scalar true)
* `[true, true; false, true]` =>
mat 2 1 (vector 1 (scalar true) (scalar false)) (vector 1 (scalar true) (scalar true))
* `[true, true, true; true, true, true]` =>
mat 2 2 (mat 2 1 (vector 1 (scalar true) (scalar true)) (vector 1 (scalar true) (scalar true))) (vector 1 (scalar true) (scalar true))

### Similarity with Simple Inductive Type

여기까지 하다 보면 Simple Inductive Type과 은근 비슷하다는 것을 느꼈을 것이다.
단지 차이점은 (matrix를 예로 들면) 모든 nat n, m에 대하여 set type인 `matrix n m` type을 귀납적으로 정의한다는 것이다.

## Parameterized Inductive Type

Parameterized Inductive Type도 variable binding이 있는데,
이 경우는 (matrix를 예로 들면) 임의의 고정된 n, m에 대하여 `matrix n m` type을 정의한다.

차이점은 "모든"과 "임의의 고정된"이다. 전자는 모든 nat에 대한 `matrix n m`,
즉 `matrix 1 1`, `matrix 1 2`, `matrix 1000 23`, ...,
요 모든 type들 대한 귀납적 construction인 반면,
후자는 n m이 뭔지는 모르지만, 어쨌든 `matrix n m` type만을 귀납적으로 정의하기 때문에,
해당 inductive type의 constructor들은 반드시 `matrix n m` type의 constructor이다.

위 `matrix`는 귀납 정의가 더 작은 size의 matrix를 사용하기 때문에,
즉 다른 n과 m에 대한 정의가 필요해서,
`matrix`는 annotated inductive type으로 정의해야 한다.

```coq
Inductive nat_list : Set :=
| nat_nil : nat_list
| nat_cons : nat -> nat_list -> nat_list.

Inductive prop_list : Prop :=
| prop_nil : prop_list
| prop_cons : prop -> prop_list -> prop_list.

Inductive list (A: Set) : Set :=
| nil : list A
| cons : A -> list A -> list A.
```

일반적인 프로그래밍에서 generic parameter와 같은 개념이다.
이 때 inductive type의 parameter가 그대로 constructor에도 적용된다.
왜냐하면 constructor만으로는 generic parameter를 알 방법이 없기 때문이다.
즉, `list : Set -> Set`, `nil : forall (A : Set), list A`,
`cons : forall (A : Set), A -> list A -> list A`.

## Induction Principle

Coq은 자동으로 type이 속한 universe에 따라 induction principle들을 정의한다.
이 중 *ident*_ind는 항상 생성되고,
임의의 prop 또는 predicate P에 대한 induction principle이다.
우리에겐 수학적 귀납법이라는 이름으로 익숙한 그것이다.

Set이나 type에 대해서는 inductive principle에서 predicate을 사용하고,
prop일 경우 prop을 사용한다.

### Example 1: `nat`

```coq
Inductive nat :=
| O
| S : forall n, S n
.
```

이 type은 두 개의 constructor를 가진다.

`O`는 그 자체로 nat의 원소인 constructor, 즉 induction의 base case.
`S`는 nat의 원소 n을 받아 또 다른 nat의 원소 `S n`을 만드는 constructor이다.

inductive type이 이렇게 정의되어 있기 때문에 induction principle은 다음과 같다.
임의의 predicate P(n) : nat -> Prop에 대하여,

* P(O)가 참이고, => H1
* 임의의 nat n에 대하여 P(n)이 참일 때 P(S n)이 참임을 증명할 수 있으면, => H2
* 임의의 nat n에 대하여 P(n)이 참이다 => conclusion

### Example 2: `word`

```coq
Inductive word :=
| dot
| B0 : word -> word
| B1 : word -> word
```

이 type은 binary word를 나타낸다.
(e.g., ".", "1.", "00.", "01.", "10.", "1010.", ...)

`dot`는 word의 끝을 나타내는 그 자체로 word의 원소인 constructor (base case).
`B0`는 word w를 받아서 또 다른 word `B0 w`를 만드는 constructor.
`B1`은 word w를 받아서 또 다른 word `B1 w`를 만드는 constructor.

따라서 inductive principle은 다음과 같다.
임의의 predicate P(w) : word -> Prop에 대하여,

* P(dot)가 참이고, => H1
* 임의의 word w에 대하여 P(w)가 참일 때 P(B0 w)가 참임을 증명할 수 있고, => H2
* 임의의 word w에 대하여 P(w)가 참일 때 P(B1 w)가 참임을 증명할 수 있으면, => H3
* 임의의 word w에 대하여 P(w)가 참이다 => conclusion

### Example 3: `list`

```coq
Inductive list (A : Type) :=
| nil : list A
| cons : A -> (list A). 
```

이 type은 nat과 비슷한데 A라는 parameter를 가진다.

따라서 inductive principle은 다음과 같다.
임의의 type A와 predicate P(lst) : list A -> Prop에 대하여,

* P(nil A)이 참이고 => H1,
* 임의의 A의 원소 a와 list A의 원소 lst에 대하여 P(lst)가 참일 때
P(cons a lst)가 참임을 증명할 수 있으면, => H2
* 임의의 list A의 원소 lst에 대하여 P(lst)가 참이다 => conclusion

### Example 4: `BTree`

```coq
Inductive BTree (A : Type) :=
| Null : BTree A
| Node : A -> BTree A -> BTree A -> BTree A.
```

이 type은 binary tree이다.

* `Null`은 null을 나타내는, 그 자체로 BTree A의 원소인 constructor이다.
* `Node`는 node로, A type value와 left/right subtree를 받아서 BTree A의 원소를 만든다.

따라서 inductive principle은 다음과 같다.
임의의 type A와 predicate P(t) : BTree A -> Prop에 대하여,

* P(Null A)가 참이고, => H1
* 임의의 A의 원소 a와 BTree A의 두 원소 left, right에 대하여 P(left), P(right)가
참일 때 P(Node a left right)가 참임을 증명할 수 있으면 => H2
* 임의의 BTree A의 원소 t에 대하여 P(t)가 참이다 => conclusion

### Example 5: `Matrix`

앞서 정의한 Matrix를 떠올려보자.

```coq
Inductive Matrix (A : Type) : nat -> nat -> Type :=
| s : A -> Matrix A 1 1
| v : forall n : nat, Matrix A n 1 -> A -> Matrix A (S n) 1
| m : forall n m : nat, Matrix A n m -> Matrix A n 1 -> Matrix A n (S m).
```

Inductive principle은 다음과 같다.
임의의 type A와 predicate P(mat) : forall n m : nat, Matrix A n m -> Prop에 대하여,

* 임의의 A의 원소 a에 대하여 P 1 1 (s a)가 참이고, => H1
* 임의의 자연수 n와 Matrix A n 1의 원소 vec에 대하여 P n 1 vec이 참일 때
P (S n) 1 (v A n vec a)가 참임을 증명할 수 있고, => H2
* 임의의 자연수 p q, Matrix A p q의 원소 mat, Matrix A p 1의 원소 vec에 대하여,
P p q mat이 참이고 P p 1 vec이 참일 때
P p (S q) (m A p q mat vec)이 참임을 증명할 수 있으면 => H3
* 임의의 자연수 n m, Matrix A n m의 원소 mat에 대하여 P n m mat이 참이다 => conclusion

### Example 6: `True`

Inductive prop에서는 P s로 썼던 부분이 P로 바뀐다.

```coq
Inductive True : Prop := I.
```

Set과 Prop에서 inductive principle의 차이를 알아보자.

```coq
(* when set *)
forall (P : True -> Prop)
    (H0 : P I),
forall t : True, P t
(* when prop *)
forall (P : Prop)
    (H0 : P),
forall _ : True, P
(* which is identical to P -> True -> P *)
```

### Example 7: `even`

```coq
Inductive even : nat -> Prop :=
| even_O : even 0
| even_SS (n : nat) (H : even n) : even (S (S n)).
```

```coq
(* when even is nat -> Set *)
even_ind: forall (P : forall (n : nat), even n -> Prop)
    (H0: P 0 (even_O))
    (H1: forall (n: nat) (p: even n), (P n p) -> (P (S (S n)) (even_SS n p))),
forall (n : nat) (p : even n), P n p
(* when even is nat -> Prop *)
even_ind: forall (P : forall (n : nat), Prop)
    (H0: P 0)
    (H1: forall (n: nat) (_: even n), (P n) -> (P (S (S n)))),
forall (n : nat) (_: even n), P n
(* which is identical to
forall (P : nat -> Prop), P 0 -> (forall n, even n -> (P n) -> (P (S (S n))) -> forall n, even n -> P n)
*)
```
