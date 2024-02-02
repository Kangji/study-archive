# Linux System Call

User process에서 hardware 작업을 직접 수행하는 것이 아니라 kernel에게 작업을 위임하는데,
이 때 system call을 이용한다.

## Stub

System call은 user stub과 kernel stub, 두 개의 stub을 거친다.
User stub은 `printf()`, `sleep()`, `exec()`과 같은 system function들이고,
내부적으로 `syscall()`이라는 function을 syscall number와 인자들과 함께 호출한다.
인자 수에 따라 `syscall2()`, `syscall3()` 등이 있다.
`syscall()`은 `trap` instruction을 통해 mode switch를 한다.

Kernel stub은 user space에서 argument를 kernel space로 복사해오고,
argument들이 valid한지 check한 후 vector table을 통해 syscall handler로 진입한다.
이후 결과를 다시 kernel space에서 user space로 복사하고 mode switch를 한다.

## Error Handling

일반적으로 UNIX system에서 library call은 error를 `errno`라는 전역변수에 저장하고,
`-1`을 return한다. 반면, syscall은 `-errno`를 return한다.
Syscall의 return value를 errno에 저장하고 -1을 return하는 작업은 user stub이 한다.
