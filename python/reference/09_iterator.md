# Iterator

파이썬에서 많은 collection type들은 iterable하다.
Iterable type의 정확한 정의는 `__iter__`의 구현 여부이다.
이 메소드는 iterator type을 return하고,
iterator type은 `__next__` method를 구현하는 type이다.

이처럼 iterable type과 iterator type은 짝을 이루고 있으며,
python built-in iterable type들은 각자 대응하는 내장 iterator type이 있다.

* `list` -> `list_iterator`
* `range` -> `range_iterator`
* `str` -> `str_ascii_iterator`
* `tuple` -> `tuple_iterator`
* `bytes` -> `bytes_iterator`
* `bytearray` -> `bytearray_iterator`
* `set`, `frozenset` -> `set_iterator`
* `dict`(.keys()), `mappingproxy`(.keys()) -> `dict_keyiterator`
* `dict.values()`, `mappingproxy.values()` -> `dict_valueiterator`
* `dict.items()`, `mappingproxy.items()` -> `dict_itemiterator`

또한 iterator type은 그 자체로 iterable하며, `__iter__` 메소드는 자기 자신을 return한다.

## Control Flow

iterable/iterator 객체들은 `for` loop에서 사용되는데, control flow는 다음과 같다.

```py
for value in iterable:
    do_something(value)

# is equal to

iterator = iterable.__iter__()
while True:
    try:
        value = iterator.__next__()
        do_something(value)
    except StopIteration:
        break
```

## Reversible Types

Iterator 개념은 단순 iterator 외에도 double-ended iterator, exact-size iterator 등 지원하는 연산에 따라 다양한 iterator 개념이 있다.

한편, 파이썬에서 reversible type은 `__reversed__`를 구현하는 type이며,
개념적으로는 역순으로 순회하는 iterator를 return한다.
이 때 역순이란 `__iter__` 메소드가 return하는 iterator 기준 역순이지만,
syntax상으로는 `__iter__`를 구현하지 않아도 가능하다.

built-in reversible iterable type과 대응하는 iterator type은 다음과 같다.

* `list` -> `list_reverseiterator`
* `range` -> `range_iterator`
* `dict`(.keys()), `mappingproxy`(.keys()) -> `dict_reversekeyiterator`
* `dict.values()`, `mappingproxy.values()` -> `dict_reversevalueiterator`
* `dict.items()`, `mappingproxy.items()` -> `dict_reverseitemiterator`

## `reversed`

Built-in `reversed` function은 reversible type의 reverse iterator를 return하는데,
내부적으로 `__reversed__`를 호출하고, 만약 `__reversed__`를 지원하지 않는다면 `__getitem(n)__`과 `__len__`을 이용하는 내장 `reversed` type 객체를 생성한다.
물론, `__getitem__(n)`과 `__len__`을 구현하는 sequence type에만 해당한다.
`str`, `tuple`, `bytes`, `bytearray`에 해당한다.

또한, 사실 `reversed` 함수와 `reversed` 클래스는 동일한 객체이다.

## Asynchronous Iterable/Iterator

파이썬이 비동기 프로그래밍을 지원함에 따라 iterator 등 다양한 type들도 비동기를 지원한다.
Async iterable은 `__iter__` 대신 `__aiter__`를 호출하여 async iterator를 return하고,
Async iterator는 기존 `__next__` 대신 비동기적으로 `__anext__`를 호출한다.

Async iterable/iterator는 `async for` loop에서 사용되고,
`async for` keyword는 `await`처럼 async 상황에서만 사용할 수 있다.
control flow는 다음과 같다.

```py
class AsyncIter:
    def __aiter__(self):
        return self
    async def __anext__(self):
        ...

async for value in async_iterable:
    await do_something(value)

# is equal to

async_iterator = async_iterable.__aiter__()
while True:
    try:
        value = await async_iterator.__next__()
        await do_something(value)
    except StopIteration:
        break
```
