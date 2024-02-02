# Lambda Calculus

Lambda calculus is a formal system in mathematical logic for expressing computation based on function abstraction and application using variable binding and substitution.
It is a universal model of computation that can be used to simulate any Turing machine.
It was introduced by the mathematician Alonzo Church in the 1930s as part of his research into the foundations of mathematics.

Lambda calculus consists of constructing lambda terms and performing reduction operations on them.

In the simplest form of lambda calculus, terms are built using only the following rules:

* $x$: A variable is a character or string that represents parameter
* $(\lambda x.M)$: A lambda abstraction is a function definition, taking as input the bound variable $x$ and returning the body $M$.
* $(M N)$: An application, applying a function $M$ to an argument $N$.

## Everything is Value

Lambda Calculus의 핵심은 모든 program = mechanically executable function = term expressed by language(which is lambda calculus)를 value로서 바라본다는 점이다.
