# Pattern Matching

Inductive type을 constructor 별로 case analysis를 통해 destruct.
각 pattern은 constructor여야 함.

```coq
match {term {as {name}}? {in {pattern}}?} {return {term}}? with
{| pattern => term}*
end

match
    match x in (eq _ H) return (@eq nat H n) with
    | eq_refl => @eq_refl nat n
    end in (eq _ H)
    return
    (@eq nat
        ((fix Ffix (x0 x1 : nat) {struct x0} : nat :=
            match x0 return nat with
            | O => x1
            | S x2 => S (Ffix x2 x1)
            end) H H)
        ((fix Ffix (x0 x1 : nat) {struct x0} : nat :=
            match x0 return nat with
            | O => x1
            | S x2 => S (Ffix x2 x1)
            end) m m))
with
| eq_refl =>
    @eq_refl nat
        ((fix Ffix (x0 x1 : nat) {struct x0} : nat :=
            match x0 return nat with
            | O => x1
            | S x2 => S (Ffix x2 x1)
            end) m m)
end
```

## Return Clause

Return clause는 match expression의 type을 explicit하게 나타내는데 사용된다.

Matched term에 non-dependent한 type인 경우 모든 branch가 같은 type을 가지고,
추측 가능하기 때문에 보통 생략한다.

Dependent case는 두 가지 subcase가 있다.

### First Subcase: `as` Clause

각 branch에서 return하는 term의 type이 matched term에 따라 달라지는 경우.

하지만 이 경우에도 대부분 return type은 유추 가능하기 때문에 보통은 굳이 안써줘도 되긴 한다.

```coq
Inductive bool : Type :=
| true : bool
| false : bool.
Inductive eq (A:Type) (x:A) : A -> Prop :=
| eq_refl : eq A x x.
Inductive or (A:Prop) (B:Prop) : Prop :=
| or_introl : A -> or A B
| or_intror : B -> or A B.

Definition b_case : forall b : bool, or (eq bool b true) (eq bool b false) :=
  fun b : bool =>
    match b as x return (or (eq bool x true) (eq bool x false)) with
    | true => or_introl (eq bool true true) (eq bool true false) (eq_refl bool true)
    | false => or_intror (eq bool false true) (eq bool false false) (eq_refl bool false)
    end.
```

### Second Subcase: `in` Clause

Matched term이 annotated inductive type인 경우.

Annotated inductive type은 constructor가 특정 annotation value에 대한 constructor이기 때문에 branch마다 construct된 term의 type이 다를 수 있다.
이 때 type들의 다른 부분은 annotation이고,
annotation에 따라 empty type인 경우 어느 branch에도 걸리지 않을 수 있다.
이는 empty inductive type에 대하여 match term을 쓰는 것과 비슷하다.

이런 annotated type 중에서 empty type이 있는 경우 match term의 return type을
유추할 수 없어서 반드시 return clause가 필요하다.
문제는 return type이 여기에 dependent할 수 있는 점이다.
이게 왜 문제가 되냐면, match term 안에서 annotation을 유추할 수 있는 방법이 전무하기 때문이다.
따라서 우리는 match clause에서 return type을 명시할 때 annotation을 나타내는 변수를
지정해주어야 한다. 이게 바로 in clause의 역할이다.

예시를 살펴보자.

```coq
Definition eq_sym : forall (A : Type) (x y : A) (H : eq A x y), eq A y x :=
  fun (A : Type) (x y : A) (H : eq A x y) =>
    match H in eq _ _ z return eq A z x with
    | eq_refl _ _ => eq_refl A x
    end.
```

물론 우리는 함수를 정의하면서 H가 eq A x y type이라는 것을 알지만, match term 안에서는 모른다.
in clause에서 annotation을 variable z에 binding 했으므로
(헷갈리지 않기 위해 공통부분인 parameter는 반드시 `_`로 표시),
match term 안에서 matched term H의 type은 `eq A x z`인 것이다.

match term의 return type은 annotation에 dependent할 수도 아닐 수도 있는데,
딱 하나 지켜야 하는 것은 당연하지만 matched term의 실제 annotation(여기서는 y)에 대해서는
return type이 맞아야 한다.

이제, constructor 부분을 살펴보자.
constructor로 construct된 type은 annotation은 constructor마다 다르겠지만,
만약 matched term이 해당 branch에 걸린다면 construct된 type의 annotation이
matched term의 annotation이라는 말이다. (leibniz equality)
이 경우 return type에 해당하는 term을 적절히 제시해야 한다.

이걸로 (2 = 3) -> False를 증명해보자.

```coq
Definition two_equals_three_then_false : (eq nat 2 3) -> False :=
  fun x : eq nat 2 3 =>
    match x in (eq _ _ y) return match y with
                          | S (S O) => True
                          | _ => False
                          end
    with
    | eq_refl _ _ => I
    end.
```

우리는 `eq nat 2 3` 에서 False로 가는 함수를 제시해야 한다.
`fun x : eq nat 2 3 =>`로 시작하자.
이제 local context에서 x는 `eq nat 2 3`의 원소이다. 실제로 존재하는지 여부는 상관없다.
그 다음 x를 match로 destruct할 것인데, eq는 annotated induction type이기 때문에,
in clause가 필요하겠다.
nat과 2는 parameter이므로 _로 표시하고 마지막 annotation만 y에 binding하자.

이제 return type을 정의할 것인데, eq_refl branch를 보면 y가 2인 경우에만 걸리고,
나머지는 empty type이다. 즉, y가 x의 실제 annotation인 3과,
eq_refl branch의 annotation인 2인 경우만 신경쓰면 된다.
y가 x의 실제 annotation인 3인 경우는 반드시 return type이 False여야 하는데,
만약 return type을 그냥 False로 해버리면 y가 eq_refl branch의 annotation인 2인
경우 term을 제시할 수 없다. 그래서 어쩔 수 없이 return type은 y에 dependent해야 하고,
단순히 생각해보면 y가 2인 경우 non-empty type (ex, True),
나머지는 False로 하면 잘 정의된다. 따라서 return type을
`match y with | S (S O) => True | _ => False end`로 정의했다.

마지막으로, eq_refl branch에서 term을 제시해주어야 한다.
이 경우 y는 2이므로 True의 element인 I를 주면 된다.

결론적으로 match term의 type은
"`eq nat 2 y` type인 x를 destruct하여 y가 2인 경우 True, 나머지는 False"
라고 말로 표현할 수 있는 x의 type에 dependent한 type이다.
그리고 우리가 `two_equals_three_then_false`의 element로 제시한 function은
`eq nat 2 3`의 원소 x를 받아서 위의 match term을 return하는 함수인데,
match term의 type은 x에 의해 False로 결정난다.
즉, 우리가 쓴 함수의 type은 `(eq nat 2 3) -> False` type이다.
따라서 well-defined.

## Extension: `if then else`

For any inductive type with two constructors (let's say, A and B),
`if term1 then temr2 else term3` is equivalent to:
`match term1 with | A => term2 | B => term3 end`.
Constructor 순서는 inductive definition에서의 순서.
