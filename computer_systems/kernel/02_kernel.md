# OS Kernel

컴퓨터 시스템이 부팅됨과 동시에 가장 먼저 메모리에 적재되어 실행되는 시스템 software.
Kernel code는 한 번 main memory에 load되고 나면 계속 상주한다.

커널은 process management & scheduling, resource management 등을 담당한다.
이처럼 hardware에 직접적인 접근 권한을 가지기 때문에 untrusted user program으로부터
hardware resource를 보호할 수 있어야 한다.

## Kernel Space

Process의 address space에서 Kernel code 및 data가 적재되는 protected address space.
Privileged instruction 및 hardware에 대한 full access가 가능하고,
user application은 system call 등 제한된 방법으로만 kernel space에 접근할 수 있다.

### Privileged Instructions

Examples:

* Change the mode
* Adjust memory access
* Disable/enable interrupt

## User Space

User application 및 user data가 적재되는 address space.
Unprivileged executions only:

* unprivileged instruction
* access to unprotected address space only

## Dual Mode

OS가 user space에서 privileged execution 막는 방법.
Mode를 나타내는 hardware register가 있어서 kernel mode에서만 privileged execution을 허용한다.
User mode에서 kernel space에 접근하려고 하면 보통 exception이 발생한다.

### Mode Switch: User $\rightarrow$ Kernel

Limited kernel entrypoints are:

* Interrupts
* Exceptions
* System Calls

### Mode Switch: Kernel $\rightarrow$ User

* Return from interrupt / exception / system call
* Context Switch
* User level upcall (UNIX signal)
