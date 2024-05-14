# Transaction Isolation

트랜젝션을 구현하는 방식은 DBMS마다 다른데, 가장 일반적인 예시는 RWLock이다.
또한 rigorous 2-phase locking으로 구현하여 conflict serializability와 recoverability를 보장한다.

그러나 이런 방식은 동시성이 매우 떨어지고, 대부분의 경우는 serializability를 보장하지 않아도 충분하기 때문에 이보다 더 느슨한 제약조건을 건다.
제약 조건이 떨어질수록 다양한 이상현상이 발생할 수 있다.
* Serialization Anomaly: 스케쥴이 serializable하지 않다.
* Phantom Read: 한 트랜젝션 내에서 동일한 쿼리를 다시 불렀을 때, 다른 트랜젝션의 커밋으로 인해 없던 data가 생겼다.
* Non-repeatable Read: 한 트랜젝션 내에서 동일한 쿼리를 다시 불렀을 때, 다른 트랜젝션의 커밋으로 인해 같은 data의 값이 달라졌다.

심한 경우는 recoverability도 포기하는데, 이때는 dirty read도 발생할 수 있다.
* Dirty Read: 다른 트랜젝션의 변경사항이 커밋되지 않았는데도 보인다.

트랜젝션간 제약조건에 따라 4가지 트랜젝션 격리수준으로 분류한다.

### Serializable

위에서 논의한 대로, serializability와 recoverability를 보장하는 격리수준.
일반적으로 rigorous 2-phase locking으로 구현 가능하다.

### Repeatable Read

Serializable하지 않을 수는 있지만, 한 트랜젝션 내에서 쿼리의 반복 호출이 같은 data에 대해 동일한 결과를 보장하는 격리 수준.
읽기 작업은 트랜젝션이 시작되는 시점에 대한 스냅샷을 바탕으로 이루어지며, 별도의 lock을 필요로 하지 않는다.
쓰기 작업을 실행할 때 해당 data에 대한 lock을 잡고, snapshot과 비교하여 값이 변하지 않았을 경우 write한다.
만약 값이 변했다면 에러를 발생시킨다.

예를 들어, BEGIN(T1) - BEGIN(T2) - WRITE(T1, A, A+1) - WRITE(T2, A, A+1) - ROLLBACK(T1) - COMMIT(T2)라는 스케쥴은
* Serializable: WRITE(T1, A, A+1)에서 T1이 A에 대한 Write lock을 잡기 때문에 존재할 수 없는 schedule이다. ROLLBACK(T1)이 WRITE(T1, A, A+1)에 선행되어야 함.
* Repeatable Read: WRITE(T2, A, A+1)는 ROLLBACK(T1)까지 block되었다가, 이후 문제없이 write된다.

### Read Committed

SQL 표준 격리 수준이며, dirty read를 제외한 다른 이상현상이 모두 발생할 수 있는 격리 수준.
읽기 작업에 대한 동시성 제어는 없으며, 현재 DB state에 대하여 그대로 수행한다.
따라서 한 트랜젝션 내에서 동일한 읽기 작업을 반복할 때 data의 값이 바뀔 수 있다.
쓰기 작업은 실행할 때 해당 data에 대한 lock을 잡고 수행하며, commit될 때 unlock하면서 DB에 반영한다.

예를 들어, BEGIN(T1) - BEGIN(T2) - WRITE(T1, A, A+1) - WRITE(T2, A, A+1) - COMMIT(T1) - COMMIT(T2)라는 스케쥴은
* Repeatable Read: WRITE(T2, A, A+1)는 COMMIT(T1)까지 block되었다가, T1이 commit되면서 값이 바뀌었으므로 error를 발생시킨다.
* Read Committed: WRITE(T2, A, A+1)은 COMMIT(T1)까지 block되었다가, T1이 commit되면서 바뀐 값을 바탕으로 쓰기 작업을 수행한다.

### Read Uncommitted

Recoverability도 보장하지 않는 격리 수준이며, 왠만해서는 사용하면 안된다.
별도의 lock을 사용하지 않는다.
