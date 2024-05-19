# Asynchronous Programming

여태까지 소개한 process/thread를 이용한 concurrency는 각 process/thread마다 PCB/TCB라는 memory overhead와 context switch라는 overhead가 발생한다는 단점이 있다.
유저 입장에서 느낄 수 없는 overhead이지만, scheduling entity가 수천 개 단위로 늘어나게 되면 슬슬 버겁게 느껴진다.
특히, lock 등 synchronization primitive들에서 더더욱 체감이 된다.

## Linux Concurrency Modelds

Process/Thread는 함수로 표현된 특정 task를 수행하는 것이 Lifecycle이다.
Linux에서 user application이 여러 task를 처리하기 위해 택하는 concurrency model은 대충 다음과 같이 분류할 수 있다.

* Iterative: Task Queue를 두고 단일 process/thread가 계속 수행하는 모델, 즉 zero-concurrency model.
* Forking: 각 task마다 task를 처리하는 별도의 process를 fork하는 모델
* Preforked: 매번 process를 fork하는 overhead를 줄이기 위해 미리 fork해놓은 process pool을 이용하는 모델
* Threaded: 각 task마다 task를 처리하는 별도의 thread를 spawn하는 모델 - process에 비해 overhead가 적다
* Prethreaded: 매번 thread를 spawn하는 overhead를 줄이기 위해 미리 spawn해놓은 thread pool을 이용하는 모델

## Green Threads

일반적으로 가장 좋은 concurrency 성능을 보이는 모델은 thread pool 모델이나,
이 또한 감당할 수 있는 concurrent task에 한계가 있다.

한편, 앞서 소개한 green threading, 즉 user-level threading은 context-switching 비용을 줄일 수 있지만,
동시에 한 user-level thread가 system call(특히 I/O 관련) 등으로 인해 block될 경우, 모든 thread가 block된다는 문제점이 있다.

## Blocking Operations

왜 특정 operation들은 thread를 block시키는가?
특히 I/O와 같은 operation들은 외부에서 수행하는 작업이 포함되어 있기 때문에 일정 clock cycle 안에 수행된다는 보장이 없다.
예를 들어, disk read는 disk에서 kernel space로 (DMA를 통해) block read를 수행하는데,
이 작업은 CPU가 아닌 disk에서 일어난다.

따라서 이런 종류의 작업들은 작업의 요청과 작업의 완료를 알리는 외부 interrupt로 구성되며,
작업 요청 후 interrupt를 받을 때까지 해당 thread를 block시킨다.

Blocking operation들은 I/O, thread/process join, synchronization, timer operation 등이 있다.

## Asynchronous Nonblocking Operations

비동기 프로그래밍은 위와 같은 synchronous blocking operation들을 asynchronous non-blocking operation으로 대체하여,
green threading의 문제점을 해결한다.

Asynchronous non-blocking operation은 asynchronous, 즉 작업의 요청과 완료(return)가 별도로 이루어지며,
non-blocking, 즉 작업의 요청 이후 완료시까지 thread를 block하지 않는다.
따라서 그 동안 다른 작업을 수행할 수 있다.

## Underneath: Polling

비동기 작업의 원리는 polling이다. 작업을 요청한 후, 작업이 완료되었는지 "poll"한다.
Polling은 완료된 작업에 대한 callback까지 포함하는 operation이다.

예를 들어서, Disk Read 요청을 한 후 완료되었는지 poll할 때,
만약 완료되지 않았다면 완료되지 않았음을 알려줄 것이고,
완료되었다면(Kernel space buffer에 data가 복사되었으면) callback으로 해당 data를 user space로 복사한 후 완료되었음을 알려줄 것이다.

## Blocking Operation in Asynchronous Context

비동기 context에서 blocking operation을 수행하고 싶을 때는 어떻게 할까?

Blocking operation을 수행하는 별도의 thread 등을 생성한 후,
해당 thread를 non-blocking으로 join하는 asynchronous task를 scheduling하면 된다.
