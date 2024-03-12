# Coroutine

파이썬에서 비동기 프로그래밍을 지원하는 type들을 **awaitable**하다고 한다.
해당 type들은 `__await__`을 구현하며, `await` keyword와 함께 사용한다.

## async/await

`await` 키워드는 아무 context에서 사용할 수는 없다.
`asyncio`, `async def` 등 async 상황에서만 사용할 수 있다.

## Coroutine

Coroutine은 가장 일반적인 awaitable object이며, 다른 언어들의 future에 해당한다.
`async def`를 통해 정의한 함수는 호출시 `coroutine` object를 return한다.

Rust처럼 coroutine은 poll(await)하지 않으면 실행되지 않는다.

## `asyncio`

파이썬 `asyncio` 라이브러리가 비동기 operation을 지원한다.
대표적인 예시는 `asyncio.run(my_coroutine)`. 이는 coroutine이 끝날 때까지 wait한다.
