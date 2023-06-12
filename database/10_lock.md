# Concurrency Control

## Lock Based Protocol

* Shared(S)
* Exclusive(X)

## 2-Phase Locking

* Growing Phase
* Shrinking Phase

### Features of 2PL

* assures conflict serializability, but
* Deadlock & Starvation Possible
* Cascading Rollback

## Avoid Cascading Rollback

* Strict 2PL: hold exclusive locks until commit/abort
* Rigorous 2PL: hold all locks until commit/abort

## Discussion 18-2. Starvation

Q. What is starvation? Can you think of a scenario in which a transaction Ti is in a starvation state but not necessarily in a deadlock state?

A. 다른 transaction들이 끈임없이 shared lock을 잡는 경우 write lock을 잡으려 하는 Ti는 계속해서 기다려야 한다.

## Discussion 18-3. Rigorous 2PL

Q. Both strict 2PL and rigorous 2PL ensure conflict serializability and cascadelessness (thus, recoverability) while both are not free from deadlocks.
Since rigorous 2PL holds both shared locks and exclusive locks until the transaction commits, it allows less concurrency than strict 2PL.
Then why do DBMSs implement rigorous 2PL at all?

A. 일반적으로 언제 growing phase가 끝나는지 알기 힘들다. Rigorous 2PL은 그걸 알 필요가 없기 때문에 구현이 더 쉽다.
