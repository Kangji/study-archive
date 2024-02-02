# Programming Language

1930년대 말부터 일군의 수학자들은 **기계적으로 계산 가능한 함수**가 무엇일까를 고민했다.
이 과정에서 프로그램과 프로그래밍 언어의 개념이 등장했다.

## Program

이들이 말한 "기계적으로 계산 가능한 함수"가 프로그램의 정의이다.
기계가 프로그램을 실행하면 이 함수를 계산하여 input에 따른 output을 return한다.

### State

경우에 따라 프로그램은 상태를 가질 수 있다.
State는 output에 영향을 미치며, 프로그램을 실행하면서 state가 바뀔 수도 있다.

## Programming Language

수학자들은 프로그램을 정의하기 위해 프로그래밍 언어를 도입했다.
프로그래밍 언어로 정의할 수 있는 것들을 프로그램이라 하였다.
프로그래밍 언어는 Syntax(형태)와 Semantics(의미)로 구성되어 있으며,
Syntax는 유효한 프로그램에 대한 규칙이고, Semantics는 프로그램의 실행에 대한 규칙이다.

따라서 모든 프로그램은 언어를 기반으로 한다.

## Computer

프로그래밍 언어가 컴퓨터를 돌리기 위해 등장한 것이라고 생각하기 쉽지만,
역사적으로 보면 프로그램과 프로그래밍 언어 개념이 먼저 등장하였다.
그리고 프로그램을 기계적으로 빠르게 계산하기 위해 등장한 기계가 바로 컴퓨터이다.

컴퓨터의 시작은 1930년대로 거슬러 올라간다.

### Turing Machine

수학자 앨런 튜링에 의해 제안된 개념으로, 오토마타의 일종이며, 앞서 말했던 임의의 "기계적으로 계산 가능한 함수"를 계산하기 위한 가상의 기계이다.
Turing machine은 **state**와 더불어 symbol을 기록할 수 있는 cell로 이루어진 무한히 긴 **tape** (syntax), 그리고 특정 state에서 특정 symbol을 읽었을 때 해야 할 행동을 정의한 **action table** (semantics)로 이루어져 있다.

튜링머신의 state, tape, action table이 각각 컴퓨터의 register, memory, ISA로 발전했다.

### Universal Turing Machine

하나의 actiom table을 가진 turing machine은 하나의 함수만 계산할 수 있다.
하지만 우리가 원하는 기계는 모든 프로그램을 계산할 수 있어야 한다.
그래서 등장한 개념이 universal turing machine이다.
Universal turing machine은 임의의 turing machine의 state set과 action table을 tape에 기록함으로 임의의 turing machine의 역할을 할 수 있다.

### Von Neumann Model

Universal turing machine을 실제로 전자공학적으로 구현해낸 사람이 폰 노이만이다.
그가 universal turing machine을 바탕으로 디자인한 hardware architecture가 **von neumann model**이며, 이 기계가 **컴퓨터의 시초**이다.

## Language Implementation

이렇게 만들어진 전자적인 계산장치, 즉 컴퓨터는 전기신호를 통해 제어하는데,
전기신호의 유무를 0과 1로 표현하는 2진법 표현이 사용되었다.
따라서 컴퓨터는 0과 1로 구성된 특정 패턴의 전기신호가 들어오면 이에 맞게 정해진 동작을 하는 형태이다.
여기서 "0과 1로 구성된 특정 패턴"을 우리는 machine language라고 부른다.
그런데 기계어는 사람이 읽기에 너무나 불편해서 기계어와 특정 기호를 1대 1로 대응시켜서 사람이 읽기에 조금 더 편한 형태로 만들었고, 그것이 assembly이다.

여기서 우리는 한 가지 문제를 마주하게 된다.
우리가 만드는 소프트웨어는 다양한 프로그래밍 언어를 기반으로 하는데, 컴퓨터는 내장된 기계어만 이해할 수 있다.

컴퓨터로 프로그램을 실행시키기 위한 두 가지 시스템이 도입되었고, 이를 **언어 구현체**라고 한다.

- Compiler: 프로그래밍 언어를 기계어로 번역하는 프로그램 (syntactic approach)
- Interpreter: 프로그래밍 언어를 직접 실행해주는 프로그램 (semantic approach)

언어는 언어 구현체에 독립적이며, 한 가지 언어를 구현하는 방식은 여러 가지일 수 있다.

### Compiler

언어 구현체로서의 컴파일러는 프로그램을 읽고, 이를 기계어로 변환한다.
하지만 넓은 의미의 컴파일러는 한 프로그래밍 언어로 쓰여진 프로그램을 다른 프로그래밍 언어로 번역하는 일을 한다.
더 넓은 의미의 컴파일러는 프로그램 뿐만 아니라, 정해진 type의 input을 받아서 정해진 type의 output으로 변환시키는 일을 한다.
이 때 input과 output은 의미적으로 반드시 동일해야 한다.

컴파일러는 보통 frontend, optimizer, backend로 이루어져 있다.

#### Frontend

Frontend는 input을 해석하고 내부적 자료구조인 intermediate representation(IR)으로 변환하는 일을 한다.

#### Optimizer

Optimizer는 IR을 최적화한다.

#### Backend

Backend는 최적화된 IR을 output으로 변환하는 일을 한다.

### Interpreter

인터프리터는 프로그래밍 언어로 쓰여진 프로그램을 기계어로 변환하는 과정을 거치지 않고, 곧바로 실행한다.
인터프리터는 소스 코드를 실행 가능한 단위로 parsing하는 단계와, 실제로 수행하는 execution 단계로 나뉜다.
이 구조 덕분에 인터프리터를 이용하면 코드를 line 단위로 수행하는 것이 가능하다. 대표적인 예시가 shell이다.

#### Java Virtual Machine(JVM)

JVM은 대표적인 인터프리터로, bytecode를 JVM specification에 맞게 실행해주는 프로그램이라고 보면 된다.
Bytecode는 machine code처럼 생겼으나, architecture에 independent해서 portability가 큰 장점이다.
CPU가 기계어를 실행하는 것처럼 interpreter가 bytecode를 실행한다.
Java 뿐만 아니라, Scala 등 다양한 언어들이 Java bytecode로 컴파일될 수 있도록 설계되어 있다.
