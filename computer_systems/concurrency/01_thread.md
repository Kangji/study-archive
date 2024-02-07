# Thread

Single execution sequence that represents a separate task.
Thread는 프로그램의 최소 실행 단위이자 하나의 작업을 나타내는 단위이다.

Thread의 최소 조건은 thread context를 저장하는 Thread Control Block(TCB)이다.
Process와 마찬가지로 State(PC & Register)와 Stack이 context에 속한다.
State와 stack 두 가지만 있으면 thread라고 한다. 즉, thread는 생각보다 general한 개념이다.

## User Thread

Thread 개념을 사용해 process를 여러 thread로 나눠서 concurrent하게 실행할 수 있다.
이 때 이 task들이 user thread가 된다.
이 개념에 따르면 signal handling도 user thread인 셈이다.

기존의 process abstraction과는 달리 여러 user thread로 이루어진 process는
user thread 수 만큼의 context를 가진다.

한편, 한 process의 user thread들은 code, data, heap address space를 공유한다.

## Kernel Thread

마찬가지로 thread 개념을 사용해 kernel을 여러 thread로 나눠서 concurrent하게 실행할 수 있다.
아니, 그래야만 한다. Kernel은 다양한 작업을 동시에 수행하기 때문이다.
이 때 이 task들이 kernel thread가 된다.

Kernel이 수행하는 작업들이 정해져 있기 때문에 kernel thread가 수행하는 작업 또한 정해져 있다.
그 예시는 process execution, interrupt handling 등이다.

Background에서 온전히 kernel 일을 수행하는 thread들은 kernel thread daemon (`kthreadd`)에 의해 생성되어 background에서 일을 한다.
이들은 예를 들면 Low-level I/O task (ex. asynchronous task on I/O device memory) 등을 한다.

예를 들어, process를 처음 시작할 때 한 kernel thread가 process를 setup하고 실행한다.
해당 kernel thread는 interrupt, exception 등을 handling하게 된다.
이후 Context switch 때 OS가 kernel space에 저장된 context를 변경한다.
이 때 개념적으로 보면 kernel thread가 switch된 것이고, kernel thread에 딸린 user process/thread가 덩달아 switch된다.

### Scheduling Entity

사실 scheduler의 scheduling entity는 kernel thread이다.
User process는 최소 하나의 kernel thread와 mapping되어 있어서 scheduling 가능하다.

User process가 최소 하나의 kernel thread와 mapping되어야 하는 이유는 interrupt, syscall을 포함한 exception handling mechanism 때문이다.
Exception handling은 kernel space에서 일어나기 때문에 kernel thread가 필요하다.

## Green Thread (1:N Mapping)

User thread를 kernel support 없이 구현한 것을 green thread라고 한다.
Kernel은 user process 내에서 일어나는 일을 알지 못하기 때문에,
이 경우 user thread는 kernel의 scheduling 대상이 될 수 없다.
즉, green thread의 scheduling이나 context switch는 모두 user level에서 일어나고,
kernel 입장에서 multiple green thread는 단일 scheduling entity(process)일 뿐이다.

User level에서 green thread간의 preemption은 signal과 timer interrupt를 활용한다.

## 1:1 Mapping

Green thread의 문제를 해결하기 위해서 user thread와 kernel thread를 1:1 mapping,
즉 user thread를 새로 생성할 때마다 kernel의 도움을 받아 kernel thread 또한 새로 생성한다.
이 때 mapping된 kernel thread는 thread 관련 kernel operation을 담당한다.

이 경우 user thread 또한 thread 단위로 scheduling이 가능하다는 장점이 있지만,
thread 생성에 kernel support가 필요하여 cost가 증가한다.

## M:N Mapping

두 가지의 절충안. n개의 user level thread를 m개의 kernel thread에 mapping한다.
하나의 kernel thread가 block될 경우 해당 thread에 mapping된 user thread는 block되지만,
다른 kernel thread에 mapping된 user thread는 여전히 실행된다.

## Linux Task

Linux는 2.6버전부터 1:1 mapping을 사용하는 POSIX thread(`pthread`)가 표준이다.
Linux에서 scheduling 단위가 task인데, kernel/user thread 및 process를 모두 나타낸다.
Process scheduling은 process의 main thread scheduling으로 볼 수 있고,
User thread scheduling은 mapping된 kernel thread scheduling으로 볼 수 있어서,
결국 linux task는 kernel thread이다.

각 Task는 `task_struct`라는 kernel data structure를 갖는데, 이게 TCB와 같은 개념이다.
TCB에 들어가는 state와 pointer to stack은 물론, pid,
pointer to parent/sibling `task_struct`s, pointer to VM table,
pointer to file table 등 PCB에 들어가는 data들의 pointer도 포함된다.

같은 process의 task들은 VM table pointer 등이 동일한 PCB를 가리킨다.
