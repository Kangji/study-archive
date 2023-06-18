# Simple Imperative Program

## Expression & Evaluation

어떤 값을 가지는 expression과 해당 expression의 값을 찾아내는 evaluation rule이 있을 때,
프로그램을 expression으로, 실행을 evaluation으로 볼 수 있다.

즉, program syntax는 expression이고, semantics는 evaluation이다.

### Evaluation as a Relation : Bigstep Operational Semantics

Evaluation을 expression과 value 사이의 relation으로 정의할 수 있다.
그러면 값이 없거나 에러 케이스가 있는 semantics를 정의할 수 있다. (ex. div by zero)

## Variable & State

expression에 변수라는 개념을 추가할 수 있다.
변수의 값을 알기 위해서 state라는 개념도 함께 추가되어야 한다.

이제, 이 expression과 evaluation은 우리가 일반적으로 아는 명령형 프로그래밍 언어가 되었다.

### Again, Evaluation as a Relation

명령형 프로그램에서 evaluation은 program과 initial, final state 사이의 relation으로 정의할 수 있다.
이 때 relation으로 정의를 함으로써 에러나 프로그램이 끝나지 않는 경우가 있는 semantics도 정의가 되었다.

## Optimization

어떤 program을 optimize하는 것은 같은 실행 결과를 가지는 다른 program으로 치환하는 것이다.

### Optimization Soundness

최적화가 완전하려면 모든 프로그램애 대하여 최적화 전후 실행 결과가 같아야 한다.
즉, equivalent해야 한다.

## Determinism

임의의 state에서 실행한 프로그램이 최대 한 가지 state에만 도달한다면 (fail할 수도 있음),
해당 semantics는 deterministic하다고 한다.

## Equivalence

언제나 실행 결과가 같은(final state가 없는 것도 포함) 두 프로그램은 Equivalent하다.
