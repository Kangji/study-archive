# Context Manager

Python context manager는 `with` statement에서 사용할 수 있는 RAII type들이다.

## Resource Acquisition Is Initialization

C에서 프로그래머가 가장 자주 범하는 실수 중 하나는 resource de-allocation이다.
더 이상 사용하지 않는 자원을 free하지 않아서 leak이 되거나, mutex 등에서는 deadlock을 일으킨다.

C++및 몇몇 언어에서는 RAII(Resource Acquisition Is Initialization)이라는 패턴을 사용한다.
특이한 이름이지만, 객체의 생성과 동시에 자원을 획득하고, 객체의 소멸과 동시에 자원을 해제한다는 뜻이다.
예를 들면, `MutexGuard` 객체는 생성시 lock acquire를, 소멸시 lock release를 실행한다.
따라서 프로그래머가 lock release를 신경쓰지 않아도 local variable인 `MutexGuard`는 scope를 벗어날 때 소멸되므로 lock release가 실행된다.

## Context Manager

Context manger는 `__enter__`와 `__exit__`을 구현하는 type들이다.
이 메소드는 `with` statement와 함께 사용되어 RAII pattern을 구현한다.

## `with` statement

파이썬에서는 `with` statement가 이와 같은 역할을 한다.
with statement는 진입시에 객체의 `__enter__`를 실행하고, 끝날 때 `__exit__`을 실행한다.
특히, SIGKILL 등 극단적인 상황을 제외하면 exception handling이나 program exit 등 그 어떤 상황에서도 `__exit__`의 실행이 보장된다는 점이 강점이다.

## Example

대표적인 사용 예시는 `File` object이다.
이 객체는 `__enter__`에서 `open` syscall을, `__exit__`에서 `close` syscall을 호출한다.
따라서 `with` statement를 사용하면 file descriptor의 close를 신경쓰지 않아도 된다.

```py
try:
    with open('data.log', 'r') as f:
        do_something(f)
except:
    pass

# is equal to (ignores UNIX Signal)

try:
    o = open('data.log', 'r')
    f = o.__enter__()  # sys_open
    do_something(f)
    o.__exit__()  # sys_close
except:
    o.__exit__()  # sys_close
```

## Asynchronous Context Manager

Context manager도 마찬가지로 비동기를 지원한다.
Async context manager는 `__enter__`, `__exit__` 대신 비동기적으로 `__aenter__`, `__aexit__`을 구현한다. 또한 `async with` statement에서 사용된다.
`async with` keyword는 `await`처럼 async 상황에서만 사용할 수 있다.

Mechanism은 context manager와 동일하다.

```py
class AsyncContextManager:
    async def __enter__(self): ...
    async def __exit__(self, *args, **kwargs): ...

async with AsyncContextManager():
    await do_something()
```
