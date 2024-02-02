# Operating Systems

Software to manage hardware resources for users and applications.

## OS Role

* Referee: resource allocation, isolation, and communication among users, apps.
* Illusionist: appears to have infinite and exclusive use of resources.
* Glue: provides UI, libraries, etc.

## Process

Processor에서 직접 수행되는 computer program에 대한 abstraction이다.
Program의 context, memory 등이 abstraction에 포함된다.

Process의 최소 조건은 process의 metadata를 저장하고 있는 Process Control Block(PCB)이다.
VM table, file table, signal, user 등이 process의 metadata에 포함된다.
Computer에서는 여러 process들이 concurrent하게 돌아가며, OS가 PCB들을 관리한다.

### Context Switch

물리적으로 한 개의 logical CPU core는 한 번에 하나의 process만을 실행할 수 있다.
따라서 (single core 상황을 가정하고) 여러 process를 concurrent하게 실행하기 위해서,
한 process가 끝날 때까지 기다리는 게 아니라, 여러 process를 번갈아 가면서 실행한다.
이 때 한 process의 실행을 중단하고 다른 process를 실행하기 위해 context switching이 필요하다.
이전 process의 context를 저장하고 다음 process의 state를 load하는 과정이다.

## Virtual Memory

실제로 존재하지 않는 가상의 memory로, main memory에 대한 abstraction이다.
Process마다 독립적인 virtual memory가 제공되며,
가상의 메모리이므로 address space는 이론적으로는 무한하지만 사실상 word size로 한정된다.
(32bit => 4 gigabytes, 64bit => 16 exabytes)
MMU에 의해 virtual memory와 physical memory 사이의 mapping이 관리된다.

## File

File은 sequence or stream of bits이다.
따라서 UNIX에서 file은 거의 모든 것을 나타낼 수 있는 abstraction이다.
External device나 network 또한 file로 표현된다.

### I/O

File에서 data를 읽거나 쓰는 작업을 I/O라고 한다.
File의 종류에 관계없이 동일한 I/O interface(syscall)를 가진다.

## OS Challenges

* Reliability: amount of time without failure (MTTF)
* Availability: portion of time that system works (MTTF / MTTF + MTTR)
* Security & Privacy
* Portability: OS API
* Performance
  * Latency / Response Time
  * Throughput
  * (Kernel) Overhead
  * (Scheduling) Fairness
  * Predictability
