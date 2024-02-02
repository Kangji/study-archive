# Shell

Shell은 kernel의 껍데기라고 해서 shell이라는 이름이 붙었으며,
user가 user application을 실행하고 관리하는 등의 작업을 도와주는 user application이다.

Shell의 동작은 UNIX 기준 shell process를 fork한 후 program을 exec한다.
즉, `fork()`와 `exec()`이라는 두 system call을 이용한다.
Window 기준 `CreateProcess()`라는 system call을 이용한다.

## Fork

Parent process와 동일한 child process를 생성하는 syscall.
Fork는 새 process를 만들기 때문에 두 번 return하는데,
parent process에서는 child process의 pid를, child에서는 0을 return한다.

### Copy-On-Write

대부분의 경우 fork하고 나서 곧바로 `exec()`을 호출하기 때문에 모든 data를 복사하는 것은 낭비이다.
그래서 parent process의 address space를 `copy-on-write`로 mark하고 나서,
이후 write operation 수행시 physical memory에서 실제 copy가 일어나도록 한다.

## Exec

Process의 address space와 process context를 kernel space를 제외하고 실행하려는 program의 context로 바꾸는 system call이다.
Code, Data 영역에 필요한 executable을 load하고, heap과 stack을 초기화하고,
PC를 program entrypoint로 설정한다.

## Foreground vs Background

Shell을 통해 program을 실행할 때 foreground 또는 background에서 실행할 수 있는데,
foreground에서 실행할 경우 shell process는 생성된 child process가 끝나기를 기다린다.
이 떄 `wait`이라는 syscall을 이용한다.
반면 background에서 실행하려면 shell command 뒤에 `&`를 붙여주면 되고,
shell process는 동작을 계속한다.

## Wait

Child process의 termination(either success or fail)을 기다리는 system call.
Child process의 exit code를 return한다. (보통 errno)

## Upcall A.K.A. UNIX Signal

Shell에서 `ctrl-c`, `ctrl-z` 등을 통해 foreground process에 signal을 발생시킬 수 있다.
이 방식은 hardware interrupt에 의해서 kernel이 signal을 발생시키는 방법이다.
`/bin/kill` 등의 프로그램을 이용할 수도 있는데,
이 방법은 system call에 의해서 kernel이 system call을 발생시키는 방법이다.
