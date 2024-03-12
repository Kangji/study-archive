# Generator

아마도 파이썬에서 iterable/iterator를 정의하는 가장 쉬운 방법은 generator일 것이다.
Generator는 `yield` expression이 있는 함수이며, 이 함수는 호출했을 때 generator iterator를 return한다. Generator iterator는 이름처럼 iterator다.
하지만 generator iterator와 generator를 혼용해서 사용한다.

## Generator Mechanism

Generator iterator는 `__next__`를 호출했을 때, yield expression을 만날 때까지 generator 함수를 실행하다가, yield에서 execution을 중단하며 해당 value를 return한다.
다시 `__next__`가 호출되면 중단한 위치부터 다시 재개되며, 만약 함수가 끝날 때까지 yield를 만나지 못하면 `StopIteration` 익셉션을 발생시킨다.

## Asynchronous Generator

Generator도 마찬가지로 비동기를 지원한다.
Async generator는 yield expression이 있는 async 함수이고, async generator iterator를 return한다. Async generator iterator는 이름처럼 async iterator다.

Async generator function은 `async def`를 사용하지만, coroutine을 return하지 않는다.
만약 coroutine을 return한다면 `async for _ in await async_gen()` 처럼 쓰여야 하기 때문으로 보인다.

Async generator iterator mechanism은 비동기인 점을 제외하면 generator와 동일하다.

```py
async def async_gen():
    yield 1

async for value in async_gen():
    await do_something(value)
```
