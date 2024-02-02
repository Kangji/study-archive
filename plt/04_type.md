# Type Theory

Type theory is a formal logical language which includes classical first-order and propositional logic, but is more expressive in a practical sense.
It is used, with some modifications and enhancements,
in most modern applications of type theory.
It is particularly well suited to the formalization of mathematics and other disciplines and to specifying and verifying hardware and software.

Type theories are also called higher-order logics, since they allow quantification not only over individual variables (as in first-order logic), but also over function, predicate, and even higher-order variables.
Type theories characteristically assign types to entities, distinguishing, for example, between numbers, sets of numbers, functions from numbers to sets of numbers, and sets of such functions.
These distinctions allow one to discuss the conceptually rich world of sets and functions without encountering the paradoxes of naive set theory.

## Idea

All entities have type, including functions.
If $\alpha$ and $\beta$ are types,
the type of functions from elements of type $\beta$ to elements of type $\alpha$ is written as $\beta \rightarrow \alpha$.

Functions that take more than one arguments can be represented in terms of functions of one argument which returns a function type.

## Why Type?

Programming language에서 type은 각 term의 expected behavior와 연결된다.
따라서 해당 term 혹은 subterm의 type에 따라 value가 달라지거나,
아예 term의 validity가 달라질 수도 있다.

Type System은 이를 통해 program의 semantics에 대한 어느 정도의 검증을 가능하게 한다.
그러나 type system을 통한 검증은 sound하지 않다.
Type checker를 통과해도 program semantics가 올바르지 않은 경우(Runtime Error)를 우리는 너무 잘 알고 있다.
반면, type system은 대게 complete하기 때문에, type checker를 통과하지 못하는 프로그램은 실행하지 않아도 의미적으로 올바르지 않음을 알 수 있다.
