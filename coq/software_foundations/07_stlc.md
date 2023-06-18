# Simply Typed Lambda Calculus

Lambda Calculus에서 term들은 base term들로부터 constructive하게 정의된다.
특히, arrow type을 가지는, 함수에 해당하는 lambda term이 lambda calculus의 대표적인 특징이다.

base term들은 아래와 같다.

* constants
* variables
* abstraction (lambda)
* application (beta-reduction)

그 외에도 conditional 등을 추가하여 constructive하게 syntax와 semantics를 정의할 수 있다.

## Components of Language

* term
* value
* reduction
* substitution
* type
* has_type

## Values

Lambda calculus에서 value인지 여부가 모호한 것이 lambda인데,
body가 value인 lambda만 value로 볼 것인지, 혹은 lambda 자체를 value로 볼 것인지.

대부분은 후자다.

## Substitution

Lambda calculus에서 application은 substitution을 통해 일어난다.
다만 abstraction에 bind된 variable은 substition 대상이 아니다.
예를 들어, $(\lambda x. \lambda x. (x + 1)) 3$은 x를 받아서 $\lambda x.(x+1)$을 return하는 abstraction에 3을 대입하는 것인데, body에 있는 모든 x는 inner abstraction에 bind된 x이지, application 대상 lambda에 bound되어 우리가 3을 대입할 x가 아니다.

## Reduction

Lambda Calculus에서 substition을 reduction rule에 추가할 수 있다.

## Typing Context

Lambda Calculus에서 type은 subterm들의 type에 dependent하다.
따라서 type을 정의할 때는 typing context가 필요하다.
context는 variable - type간 partial mapping이다.

즉 lambda calculus에서 type은 context - term - type간 ternary relation이다.

## Closed

즉, free variable이 없는 term을 closed term이라고 한다.
free variable은 context에 없는 variable을 말한다.

## Propositions over STLC

### Canonical Form of Types

각 type마다 normal form들이 있는데, 이들을 그 type의 canonical form이라 한다.

증명은 type과 value에 대한 induction & inversion.

### Progress

empty context에서 well-typed인 모든 term은 value이거나, reducible하다.

증명은 type derivation에 대한 induction. value인 것은 자명하고,
value가 아닌 것들은 induction hypothesis들을 destruct 해보면 subterm들이 reducible하면
본 term이 reducible하므로 OK (이 때 이게 성공하려면 reduction semantics가 잘 정의되어야 한다),
다 value이면 각 subterm들을 canonical form으로 치환하면 reduction rule에 알맞은 syntax가 나온다.

term에 대한 induction도 가능하다. 증명은 매우 비슷함.

### Weakening Lemma

context gamma가 context gamma'에 포함된다면, 즉 임의의 변수 x에 대하여 gamma에서 x의 type이 존재한다면 gamma'에서도 x의 type이 같은 type이라면, 임의의 term t에 대해서 gamma에서 t의 type이 존재한다면 gamma'에서도 t의 type이 같은 type이다.

증명은 type derivation에 대한 induction.

### Substitution Preserving Lemma

모든 term은 올바른 type의 값이 변수에 대입되면 type이 바뀌지 않는다.

증명은 term과 type derivation에 대한 induction & inversion.

### Preservation

Weakening Lemma와 Substition Preserving Lemma를 이용해서 Preservation을 증명한다.

#### Preservation의 역은 성립하지 않는다

Invalid type term에 대한 reduction rule이 존재할 수 있기 때문.

### Type Soundness

empty context에서 well-typed인 term은 여러 step reduction 후에도 well typed이다.

증명은 multistep에 대한 induction. base case는 progress, induction case는 preservation.

### Type Uniqueness

모든 term은 최대 하나의 type을 가진다.

증명은 type derivation에 대한 induction. Induction hypothesis가 예쁘다.

### Free in Context

Free variable이 있는 term이 어떤 context에서 type을 가진다면 해당 context가 free variable에 대한 type을 제공한다. 따름정리로, empty context에서 well-typed term은 closed term이다.

Free variable appearance에 대한 induction으로 증명.

### Context Invariance

어떤 term이 어떤 context에서 type을 가질 때, 모든 free variable에 대해서 같은 type을 제공하는 다른 context가 있다면 해당 context에서도 같은 type을 가진다.

증명은 type derivation에 대한 induction.

## Extended STLC

Basic building block 위에 새로운 language context를 쌓아보자.

* Let Binding
* Unit Type
* List Type
* Sum Type
* Pair Type

### Fixpoint

fixpoint는 local context에서 자기 자신을 이용하기 위해서
lambda를 받아서 같은 type의 lambda를 뱉는 lambda로 만든다.

예를 들어, fun n => if n = 0 then 1 else fact (n - 1)과 같은 형식은
lambda로 만들 수 없다. 왜냐면 fact가 주어지지 않았기 때문이다.

따라서 fun f => fun x => if n = 0 then 1 else f (n - 1)로 만들면 된다.
물론 이 lambda는 원래 원하던 T1 -> T2 type이 아니라 (T1 -> T2) -> (T1 -> T2) type이다.

그러면 어떻게 사용하는가? 그것이 바로 fixpoint이다. recursion을 한 step 풀어주는 것이다.
fix fact가 우리가 원하던 재귀함수가 된다.

```txt
(fix fact) 2
--> (fact (fix fact)) 2
--> (fun x => if n = 0 then 1 else (fix fact) (n - 1)) 2
--> if 2 = 0 then 1 else ((fict fact) 1)
--> (fict fact 1)
--> ...
```

### Record

Record는 list처럼 생각할 수 있다. 그런데 이렇게 하면 induction principle이
list에 대해서는 생성되지 않아서 의도한 대로 만들어지지 않는다.
따라서 직접 만들어서 사용한다.
