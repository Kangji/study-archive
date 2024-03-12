# Global Interpreter Lock

CPython은 내부적으로 reference counting과 garbage collection을 통해 자원을 관리한다.

## Garbage Collection

C에서는 dynamic allocation을 통해 user가 명시적으로 memory를 할당하거나 해제해주어야 하지만,
Java나 Python 등에서는 **garbage collector**라는 background thread가 주기적으로 reference count를 확인하고 불필요한 메모리를 해제해준다.

## Reference Count

모든 instance는 **reference count**라는 필드를 가진다.
따라서 객체가 생성되고 소멸할 때마다 그 객체가 referencing하는 모든 인스턴스의 reference count를 늘리거나 줄여야 한다.

## Race Condition

Single threaded program에서는 문제가 없지만, multithreading에서는 이야기가 달라진다.
여러 thread에서 동시에 한 instance의 reference count를 건드리는 경우는 꽤 자주 있을 것인데,
이 경우 race condition이 발생한다.
이를 막으려면 모든 instance의 ref cnt에 대한 lock이 있어야 하는데,
lock acquire/release는 cost가 커서 결과적으로 엄청난 성능 저하를 유발하게 된다.
게다가 수많은 lock을 만들 경우, deadlock이 발생할 가능성이 높다.

## Conclusion

CPython은 이런 문제를 해결하기 위해 python interpreter 자체가 lock을 지니도록 하고, 각 thread가 interpreter에 의해 실행되기 위해서는 반드시 이 lock을 잡아야 하도록 설계되었다.
이 lock이 바로 **GIL**이다.
GIL의 문제점은 multicore 환경에서 발생하는데, core가 여러 개여도 GIL 때문에 한 번에 한 thread만 수행될 수 있어서 single core 환경과 차이가 없다.

간혹 Python이 GIL 때문에 multithreading이 오히려 성능을 저하시킨다는 오해가 있는데,
여전히 I/O-bounded program은 multithreading의 이점을 누릴 수 있어서 옳지 않다.
