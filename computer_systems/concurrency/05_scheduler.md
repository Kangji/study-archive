# Resource Scheduling

* Resource: Process/Thread가 소유 및 사용하는 "자원" (CPU time, Memory, Disk Space, Network Bandwidth 등)
* Allocation: Process/Thread가 어떤 자원을 얼만큼 소유할 것인지
* Scheduling: Process/Thread가 어떤 자원을 얼마나 오랫동안 소유할 것인지

Resource allocation & scheduling은 "policy"이다.

## Resource Preemption

* Preemptible: OS가 process/thread로부터 뺏을 수 있는 자원 (i.e., CPU time)
* Non-preemptible: OS가 뻇을 수 없고, 스스로 yield해야 하는 자원 (i.e., Disk space)

## Dispatcher

Scheduler는 자원의 할당과 스케쥴링 "정책"을 결정하는 요소이다.
반면, dispatcher는 스케쥴러가 결정한 정책에 따라 실제로 자원을 할당하는 "매커니즘"을 구현하는 요소이다.

## CPU Scheduling Algorithm

CPU scheduling algorithm은 task(process/thread)에 할당하는 CPU time의 양과 순서를 결정한다.
Scheduling algorithm이 고려해야 할 metric들은 다음과 같다.
* Min waiting time: Task들이 ready queue에서 너무 오래 기다리지 않아야 한다.
* Max CPU utilization: CPU가 idle state에 있는 건 낭비다. CPU는 할 일이 있는 한 항상 바빠야 한다.
* Max throughput: 같은 시간에 처리하는 task의 양이 많을수록 좋다.
* Min response time: Task는 생성되고 나서 가능한 한 빨리 처리해야 한다.
* Fairness: 각 task는 공평하게 처리해야 한다.

### Basic Scheduling Algorithms

* First-Come, First-Serve(FCFS) a.k.a. FIFO
    * Waiting time depends on arrival -> long job이 먼저 도착하면 전체적인 waiting time과 response time이 나빠짐
* Shortest Job First(SJF)
    * Minimize average waiting time(non-preemptive에서 optimal), 그러나 burst time을 예측하기 힘들다
    * Long job might starve
* Shortest Remaining Time First(SRTF)
    * Minimize average waiting time(optimal)
    * 마찬가지로 burst time을 예측하기 힘들어 실용적이지 않다.
* Round-Robin(RR): 각 프로세스를 일정 time slice만큼 실행하고 나서 preempt => ready queue의 맨 뒤로 보내서 round-robin
    * 적절한 time slice를 결정하는 것이 중요
    * Time slice가 작으면 context-switch overhead가 증가
    * Time slice가 크면 그냥 FCFS

### Real-Time Scheduling Algorithms

Real-time task들은 time constraint가 있다.
* Hard real-time(a.k.a. deadline)
* Soft real-time

따라서 각 task들은 위 경우처럼 서로 다른 priority를 가질 수 있다.
Scheduler는 각 task의 priority를 고려하여 scheduling한다.
예를 들어, RR은 모든 task의 priority를 동일하게 취급하는 예시이다.
또한, priority는 task가 생성된 후에도 동적으로 바뀔 수 있다.

Real-time scheduling algorithm들은 다양한 방법으로 priority를 반영한다.
* Rate Monotonic Scheduling
    * priority = 1/period => periodic task에만 적용 가능
* Earliest Deadline First(EDF)
    * priority = deadline

## Max-Min Fairness

공평함은 무엇인가? 어떤 것이 공평한 것인가?
Max-min fairness는 minimum allocation을 최대화하는 방법이다.

1. 모두에게 공평하게 분배
2. 잉여분(필요한 만큼보다 더 많이 할당받았은 경우)을 반환후 아직 더 필요한 사람들에게 공평하게 분배
3. 분배가 끝날 때까지 2를 반복

## Priority Inheritance

한편, synchronization이나 join 등으로 task간 dependency가 존재한다.
High priority task가 low priority task에 의존할 경우 문제가 생길 수 있다 (ex. deadlock).
이럴 경우 low priority task가 high priority task의 priority를 이어받는 식으로 해결한다.
이를 priority inheritance라고 한다.
