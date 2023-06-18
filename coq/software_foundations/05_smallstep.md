# Smallstep Operational Semantics

지금까지 논한 programming language semantics는 big step,
즉 어떤 state에서 프로그램 실행이 완료되면 어떤 state가 되는지였다.

이런 식의 논의는 concurrency를 표현할 수 없다.

이와 대조되는, instruction 단위의 fine-grained step-by-step reduction semantics를
smallstep semantics라고 한다.

따라서 smallstep reduction은 **term t1을 한 번 reduce하면 t2가 된다**는 relation이다.

## Value

우리가 expression의 evaluation result를 생각하듯이 term들 중에는 값에 해당하는 것들이 있다.

## Normal Form

더 이상 reduce할 수 없는 term들을 normal form이라고 한다.

## Normal Form of a Term

Deterministic한 relation을 계속 reduction해서 normal form에 도달했다면,
그 normal form을 해당 term의 normal form이라고 한다.

당연하지만 normal form은 deterministic하다.

## Equivalence Between Normal Form and Value

Semantics를 어떻게 정의하냐에 따라 normal form과 value는 같을 수도 다를 수도 있다.
혹은 서로 포함관계가 아닐 수도 있다.

## Stuck

어떤 term을 reduce하다 normal form에 도달했는데, value가 아니라면 stuck됐다고 한다.

## Multistep

Single-step semantics를 사용해서 multistep semantics를 정의할 수 있다.
multistep relation은 0번 이상의 reduction을 의미한다.

따라서 당연하지만 reflexive, transitive하고, single-step semantics를 포함한다.
