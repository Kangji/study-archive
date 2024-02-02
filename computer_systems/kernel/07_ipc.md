# Interprocess Communication (IPC)

Process간 data를 주고받는 방법은 shared memory, pipe, UNIX domain socket 등이 있다.

이 세 가지 방법은 모두 process 입장에서는 file abstraction으로 감싸져 있다.

## Shared Memory

여러 process의 virtual memory(일부)를 같은 physical memory로 mapping.

## Pipe

Kernel space에 kernel buffer(FIFO)를 두어 communicate

## UNIX Domain Socket

TCP Socket과 동일한 interface를 가져 socket이라고 부르나,
실제로는 network interface를 거치지 않고 kernel에서 data transmission을 전부 처리한다.
