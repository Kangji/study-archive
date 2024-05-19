# Atomic Instruction

Atomic instructions are guaranteed to be atomic, even in multicore system.
They temporarily disable interrupts.

## Load

Return the value.

## Store

Update the value.

## Test and Set

Write 1 (set) and return old value.

## Compare and Swap

Swap the value with new value only if the value equals to an expected value.

## Fetch and Add

Increment the value and return old value.

## Modification Order

Atomic operations requires every thread to agree with **modification order**.
수정 순서는 어떤 객체의 값을 실시간으로 확인할 수 있는 무언가가 있다고 했을 때 기록한 해당 객체의 값의 순서를 의미.

Cache 등으로 인해 모든 thread가 동일 시점에 모두 동일한 값을 관측하는 것은 사실상 불가능하다.
C++에서 보장하는 것은 atomic operation들은 모든 thread가 동일 객체에 대해 동일한 수정 순서를 관측할 수 있다는 것이다.
만약 수정 순서가 5->8->3이라면, 5->3, 8->3으로 관측할 수는 있어도 8->5는 불가능.
