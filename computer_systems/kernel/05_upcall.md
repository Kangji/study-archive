# Upcall (UNIX Signal)

Exceptional control flow의 일종으로 interrupt와는 다르게 user level에서 처리한다.
SIGINT(`Ctrl + C`) 등이 이에 해당한다.

Interrupt와의 차이점은 그 외에도 interrupt는 timer interrupt나 keyboard interrupt 등
user application에 무관하다는 것, 그리고 kernel이 처리해야 한다는 것 정도.
반면 upcall은 user application의 일부이며, application마다 동작 또한 다르다.
따라서 kernel 입장에서는 signal handler는 user process(thread)로밖에 안보인다.

Upcall은 kernel에 의해 발생할 수도 있고 다른 프로세스에 의해 발생할 수도 있다.
(아무거나 죽이진 못하고, child process에게만 가능)
signal은 PCB에 저장되고, kernel mode에서 user mode로 돌아올 때 pending signal을 처리한다.

## Signal Stack

별도의 user thread로 생각할 수 있기 때문에 stack이 필요한데, 이를 signal stack이라 한다.
당연하지만 user space에 있다.
