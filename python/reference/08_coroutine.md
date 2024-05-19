# Coroutine

파이썬에서 비동기 프로그래밍을 지원하는 type들을 **awaitable**하다고 한다.
해당 type들은 `__await__`을 구현하며, 이 메소드는 비동기 객체가 실행이 완료되기를 기다린다.

## Coroutine

Coroutine은 특정 시점에 자신의 실행과 관련된 상태를 어딘가에 저장했다가 나중에 다시 실행할 수 있는 특별한 함수이다.
Coroutine은 가장 일반적인 awaitable object이며, coroutine을 await할 경우 해당 coroutine을 실행한다.
파이썬은 coroutine을 generator를 이용하여 구현한다.

## Task and Future

Coroutine이 함수에 대응한다면, task는 thread에 대응하는 scheduling entity이다.
Thread가 함수를 실행하듯이, task는 coroutine을 실행한다.

한편, future는 어떠한 작업의 실행 상태와 결과를 저장하는 awaitable 객체이다.
Coroutine은 그 자체로 어떤 비동기 작업을 나타내는 반면,
future는 작업의 실행과 관련된 정보는 없다.
대신, future를 await할 경우 해당 객체와 연관된 작업이 끝날 때까지 기다리며,
작업이 끝난 후 결과와 함께 등록된 callback을 실행시킨다.

## async/await

Coroutine은 poll(await)하지 않으면 실행되지 않는다.
한편, `await` 키워드는 아무 context에서 사용할 수는 없다.
`async def` 등 async 상황에서만 사용할 수 있다.

`async def`를 통해 정의한 함수는 호출시 `coroutine` object를 return한다.
즉, coroutine을 poll(await)할 수 있는 것은 또 다른 coroutine이나 task 뿐이다.

사실 `async`/`await`은 generator를 이용하여 coroutine을 구현하는 syntactic sugar에 지나지 않는다.

## `asyncio`

파이썬 `asyncio` 라이브러리는 user-level thread(green thread) library이다.
Task가 바로 asyncio의 user-level thread이다.
대표적인 예시는 `asyncio.run(my_coroutine)`. 이는 coroutine을 실행하는 task가 끝날 때까지 wait한다.

### Event Loop

Asyncio는 event loop라는 매커니즘을 사용하여 task를 스케쥴링한다.
Event loop는 내부적으로 task queue를 가지고 있는데,
이는 OS scheduler의 runqueue에 대응한다.

한편, `asyncio` green threading은 1:N 모델이다.
즉, event loop가 단일 thread에서 실행된다.
단일 thread에서 event loop는 task queue에서 실행할 task를 pop하여 실행하는 것을 반복한다.

### Task Yield

한편, task들은 asynchronous non-blocking I/O나 asyncio.sleep 등 코루틴의 실행을 곧바로 이어갈 수 없는 상황을 마주할 수 있다.
Thread들은 이럴 경우 block되면서 OS scheduler의 wait queue로 이동하고, event를 기다린다.
그러나 event loop에서는 실행을 멈추고, 해당 작업에 대한 future 객체를 생성하여 callback을 등록한다.
향후 요청한 작업이 완료되면 future의 callback이 실행되어 task가 task queue로 돌아올 수 있다.

I/O의 경우 생성되는 future는 I/O에 관여하는 file descriptor를 감싸는 객체이다.
이 future는 향후 `select()` system call에 의해 해당 file descriptor가 ready상태가 되면 결과가 업데이트되며 callback이 실행된다.
반면, sleep의 경우 생성되는 future는 event loop 내부의 timer에 의해 결과가 update되며 callback이 실행된다.

### Selector

위 경우에서 `select()` system call은 분명히 blocking call이다.
따라서 event loop에서 아무때나 실행될 수는 없고, task queue가 비어있을 때 호출한다.
