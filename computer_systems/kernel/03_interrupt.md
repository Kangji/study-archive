# Interrupt

예기치 않은 문제가 발생했을 때 실행중인 프로세스를 잠시 멈추고 문제를 처리한 후 다시 돌아오는데,
이 때 예기치 않은 문제를 interrupt라고 한다.
주로 hardware timer나 I/O device 등 하드웨어에 의한 interrupt를 뜻하지만,
좀 더 포괄적인 의미로 system call을 사용한 software에 의한 의도적 interrupt도 포함하는데,
이 때 두 가지를 구분지어서 hardware interrupt와 software interrupt라고 부른다.
정리하면 software interrupt라고 하는 것의 정체는 system call이다.

Exception과의 차이점은 exception은 division by zero 등 instruction 실행 중 발생하는
것으로 synchronous한 반면 interrupt는 hardware에 의해 발생하여 asynchronous하다.

Interrupt라는 용어를 더 넓은 의미로 사용할 땐 exception도 interrupt에 포함시키기도 한다.

User process 입장에서는 달라지는게 없어서 interrupt에 대해서 아무것도 알 수 없다.

## Hardware Timer

일정 시간마다 주기적으로 interrupt를 발생시키는 하드웨어.
시간을 측정하기 위한 장치로, task가 time slice를 모두 소진한 경우 context switch를 발생시킨다.

## IRQ

Interrupt Request Line을 통해 processor에게 전달되는 signal이 interrupt request이다.
IRQ는 문맥에 따라 interrupt request line을 나타내기도,
interrupt request를 나타내기도 한다. 특히, software IRQ는 softirq라고 별도로 분리되어 있다.
분리되어 있는 이유는 hardware interrupt는 asynchronous하기 때문이다.

## Interrupt Vector Table

IRQ를 통해 interrupt request가 processor에 도착하면 processor는 실행중인 process
state를 저장하고 interrupt handler를 실행히킨다.
이 때 interrupt request에 따라 다른 interrupt handler를 실행해야 하는데,
해당 interrupt handler의 위치를 interrupt vector table에서 찾을 수 있다.
Interrupt vector table은 interrupt handler의 function pointer를 저장한다.
Interrupt뿐만 아니라 syscall handler, exception handler도 찾을 수 있다.

이처럼 Memory를 사용하면서까지 interrupt handler entrypoint를 array로 저장하는 이유는,
Interrupt handler는 빠르게 수행되어야 하기 때문이다. (If-else는 너무 느리다)

Interrupt handler를 ISR(Interrupt Service Routine)이라고도 한다.

## Interrupt Stack

A.K.A. Kernel stack.
Kernel space에 있고, kernel handler를 실행하기 위한 stack.
(Including interrupt handler, syscall handler, exception handler)

Traditionally, 1 user stack (single thread case) + 1 kernel stack per process
그런데 사실 process마다 kernel stack이 있을 필요 없이 processor마다 1개씩 있어도 충분하다.

## Interrupt Masking

Interrupt is disabled while running interrupt handler.
OS kernel can also disable interrupt for other reasons, i.e., synchronization.

이처럼 interrupt는 block되거나 interrupt되지 않고 수행되기 때문에 빠르게 수행되어야 한다.
만약 오래걸리는 작업은 background worker (PID 2 `kthreadd`가 spawn)에게 위임하여
비동기적으로 처리할 수 있다.

## Interrupt Handling Procedure

1. Mask interrupt
2. Switch to kernel stack. 아래 작업을 atomic하게 수행
    * Change PC, Stack Pointer(SP), Process Status Word(PSW) (Condition code)
    * 기존 PC, SP 등을 kernel stack에 저장
    * Change Memory protection
    * Change kernel/user mode
3. ISR 수행
4. Switch to user stack. 2번 과정을 정확히 반대로 atomic하게 수행
