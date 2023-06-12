# Recovery System

## Failure Classification

* Transaction failure
  * Logical Error: Error condition 때문에 transaction abort
  * System Error: Deadlock 등 system level에서 문제 때문에 transaction abort
* System Crash: a power failure or other hardware or software failure causes the system to crash.
* Disk Failure
  * a head crash or similar disk failure destroys all or part of disk storage
  * Destruction is assumed to be detectable(i.e., checksum)
  * Fail-stop assumption: system crash does not corrupt disk

## Storage Classification

* Volatile storage: does not survive system crash
* Non-volatile storage: survive system crash
* Stable storage: a theoretical storage that survives all failure

## Log

* a sequence of log records that describe update activities on the database
* assume that log is written directly to stable storage

## Log Records

* Transaction Starts : `<Ti start>`
* Before Ti writes X : `<Ti X v1 v2>` => write X change v1 to v2
* When Ti partially committed : `<Ti commit>`
* When Ti aborted (and also finished rollback process) : `<Ti abort>`

## Undo & Redo

* Both operation must be **idempotent**

## Checkpoint

* 이미 stable storage에 반영된 transaction은 recovery 대상이 아님
* Checkpoint 시점에 active transaction list를 함께 기록

## Transaction Rollback During Uptime

1. Scan log backward
2. For each write log `<T, X, V1, V2>`, write log `<T, X, V1>`
3. `<Ti Start>` 만나면 `<Ti Abort>`

## Recovery after System Crash

1. Redo Phase
    * Checkpoint부터 undo list = list of active transaction으로 시작
    * Redo writes
    * Start 나오면 add to undo list
    * Commit / Abort 나오면 remove from undo list
2. Redo Phase 끝난면 안끝난 transaction만 남음 => Undo Phase
    * 끝에서부터 거꾸로 보면서 undo list에 있는 write들 undo action

## Log Buffering

* Log records are buffered
* log force operation performed to commit a transaction.

## Write Ahead Logging

Commit이나 output 전에 log가 stable storage에 기록되어야 한다.

1. Log records are output to stable storage in the order in which they are created.
2. Transaction Ti enters the commit state only when the log record `<Ti commit>` has been output to stable storage
3. Before a block of data in main memory is output to the database, all log records pertaining to data in that block must have been output to stable storage.

## Discussion

### 19-1. Storage Classification

Q. Describe the difference between a non-volatile storage and a stable storage.

A. Stable storage는 이론상 system failure 및 각종 disk failure로부터도 살아남는다.

### 19-3. Deferred Modification

A DBMS may employ deferred-modification where updates of a transaction are written to the DB only after the transaction commits.

Q. How would you utilize the log to implement such strategy?

A. 실제로 값이 바뀌지 않았으므로 write할 값만 적어주면 된다.

Q. What could be the benefits for the recovery system?

A. Redo: 모든 operation을 순차적으로 따라가지 않고 마지막 write log만 보고 write 해주면 된다. Undo: 아무것도 하지 않아도 된다.

### 19-4. Concurrency

Q. Consider the situation where transactions are executed concurrently.
Should we have separate log for each transaction or should we have one single log shared by all transactions?

A. Single log. separate해놓으면 Transaction간 순서를 알 수 없다
