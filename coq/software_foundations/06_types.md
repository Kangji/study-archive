# Type System

Smallstep Operational Semantics에서 term들에 type을 부여할 수 있다.
허나, 반드시 모든 type을 가져야만 하는 건 아니다.

Type semantics에 따라 type이 존재하지 않는 term도 존재할 수 있다.

## Well-Typed

Type이 존재하는 term을 well-typed라고 한다.

## Progress

Operational Semantics와 Type Semantics가 잘 정의되었다면,
모든 well-typed term은 value이거나 reduction이 가능하다.

## Preservation

Operational Semantics와 Type Semantics가 잘 정의되었다면,
모든 well-typed term은 reduction 후에도 type이 변하지 않는다.

## Type Uniqueness

Operational Semantics와 Type Semantics가 잘 정의되었다면,
모든 well-typed term은 정확히 하나의 type을 갖는다.

## Type Checking

Type semantics를 따라 type inference를 해 나가는 과정이 type checking이다.

증명은 type에 대한 induction. fixpoint이므로 match term을 전부 destruct하자.
