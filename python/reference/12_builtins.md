# Built-in Types

`File` 등과 같이 systems programming과 관련된 내용은 제외했다.

## Numeric Types

Numeric type들은 수를 나타내는 객체들을 표현하는 type들이다.
모든 numeric object들은 불변 객체이다.

Numeric type들은 특이한 인스턴스 생성 규칙을 가진다. 이유는 잘 모르겠다.
그러나 instance들이 불변이므로 name(literal)에 대응하는 객체가 어떤 객체인지는 중요하지 않다.

```py
>>> id(1.0)
4316292912
>>> # x op y style float literal with value 1.0 are identical
>>> id(1.0 + 0.0)
4316295760
>>> id(2.0 - 1.0)
4316295760
>>> id(0.0 + 1.0)
4316295760
>>> # x op y op z style float with value 1.0 are identical
>>> id(1.0 + 0.0 + 0.0)
4317799088
>>> id(2.0 - 1.0 + 0.0)
4317799088
>>> id(0.0 + 1.0 + 0.0)
4317799088
>>> # Assigning literal constructs new numeric object
>>> x = 1.0
>>> id(x)
4317797360
>>> y = 1.0 + 0.0
>>> id(y)
4317799248
>>> a = 1.0
>>> id(a)
4316292528
>>> # Assigning numeric type object does not construct new object
>>> b = a
>>> id(b)
4316292528
```

### `float`

`float` object의 machine level 구현은 기본적으로 double-precision이다.
`float` type은 `builtins` 모듈에 있다.

Language parser가 정수 literal과 실수 literal을 구분하는 방법은 소숫점의 유무이다.

### `complex`

`complex` object는 machine level에서 단순히 두 double-precision 부동소수로 이루어져 있다.
`z.real`, `z.imag`로 `z`의 실수부와 허수부에 해당하는 `float` type 객체를 얻을 수 있다.
`complex` type은 `builtins` 모듈에 있다.

Languge parser가 복소수 literal을 인식하기 위해서는 허수부 `j`가 필요하다 (e.g, `3j`).

## Integral Types

Integral type들은 정수형 객체들을 표현하는 type의 분류이다.
모든 integral object들 불변 객체이다.

Integral type은 singleton이다. 즉, 값이 같으면 실제로 같은 object이다.

### `int`

파이썬은 data size에 무관(정확히는 Virtual Memory가 허용하는 범위까지)하게 `int` type 단 하나만 존재한다. 당연하지만, 2's complement로 정수를 표현한다.
`int` type은 `builtins` 모듈에 있다.

### `bool`

참, 거짓을 나타내는 `True`와 `False` object가 `bool` type의 instance이다.
두 object는 `builtins` 모듈에 있으며, 불변 객체이다.

`bool` type은 인스턴스가 `True`와 `False` 단 두 개만 존재하며,
`bool` type 또한 `builtins` 모듈에 있다.

## Sequence Types

파이썬에서 sequence type들은 다음과 같이 정의된다.

* 길이가 유한하다
* Iterable(순서가 있다)
* Subscriptable(`__getitem__`)이며 `int`/`slice`로 indexing할 수 있다

따라서 `len()` 함수를 통해 길이를 알아낼 수 있고, slicing이 가능하다.
Sequence 객체들은 불변일수도, 가변일수도 있다.

* 불변: `str`, `tuple`, `bytes`, `range`
* 가변: `list`, `bytearray`

### `str`

파이썬은 문자와 문자열을 구분짓지 않고 모두 문자열 `str`로 취급한다.
Quote, 또는 double quote로 string literal을 나타낼 수 있다.
`str` type은 `builtins` 모듈에 있다.

### `tuple`

일반적인 tuple type이다. 생성자 외에도 괄호로 instance를 생성할 수 있다.
`tuple` type은 `builtins` 모듈에 있다.

### `bytes`

`str`이 ascii 문자열이라면 `bytes`는 byte string이다.
Type이 `bytes`인 literal들은 `b'abc'`처럼 표현한다.
`decode()` 함수를 통해 같은 내용의 `str` type인 object를 얻을 수 있다.
`bytes` type은 `builtins` 모듈에 있다.

### `range`

주로 `for i in range(n)`에서 사용되는 `range`는 integer sequence이다.
`range(start, stop, step)`은 C언어의 `for(int i = start; i < end; i += step)`처럼 동작하는 것을 의도한 클래스이다.
`range` type은 `builtins` 모듈에 있다.

### `list`

파이썬에서 list는 가변배열, 즉 벡터이며 array of pointer로 구현되어 있다.
생성자 외에도 대괄호로 instance를 생성할 수 있다.
`list` type은 `builtins` 모듈에 있다.

### `bytearray`

가변인 점을 제외하면 `bytes` type과 일치한다.
`bytearray` type은 `builtins` 모듈에 있다.

## Set Types

파이썬에서 set type들은 다음과 같이 정의한다.

* 길이가 유한하다
* 순서가 <u>없다</u>
* 원소가 hashable하고 중복이 없다
  * Immutable hash(`__hash__`) value
  * Comparable(`__eq__`) with other objects
  * Two objects compared equal must have the same hash value.

따라서 `len()` 함수를 통해 길이를 알아낼 수 있다.
Set 객체들은 불변일수도, 가변일수도 있다.

* 가변: `set`
* 불변: `frozenset`

### `set`

일반적인 set type이다. 생성자 외에도 중괄호로 instance를 생성할 수 있다.
`set` type은 `builtins` 모듈에 있다.

### `frozenset`

불변인 점을 제외하면 `set` type과 거의 일치한다.
`frozenset` object는 심지어 hashable이기 때문에 dictionary key로도 사용 가능하다.
`frozenset` type은 `builtins` 모듈에 있다.

## Mapping Types

파이썬에서 mapping type들은 다음과 같이 정의한다.

* 길이가 유한하다.
* 순서가 없다.
* key object들과 각 object를 mapping한다.
* Subscriptable이며 key object로 indexing한다.

### `dict`

`dict` type의 instance는 key가 hashable해야 한다.
가변이고, pair가 들어온 순서를 보존한다.
`dict` type은 `builtins` 모듈에 있다.

### `mappingproxy`

불변인 점을 제외하면 `dict` type과 유사하다.
모든 instance는 `__dict__` attribute를 가지는데,
statically defined type(e.g, built-in class)들은 불변이므로,
`__dict__`가 `dict` type이 아니라 `mappingproxy` type인 점이 주목할 만하다.
불변이기 때문에 실제 구현은 더 효율적인 구조로 되어 있다.
`MappingProxyType` type은 `types` 모듈에 있는 alias이다.

참고로 `types` 모듈에 있는 (거의) 모든 type은 python standard library에는 없는 internal type들의 alias다. `mappingproxy`라는 이름은 python level에서는 어디에도 존재하지 않는다.

```py
>>> type(object.__dict__)
<class 'mappingproxy'>
>>> types.MappingProxyType
<class 'mappingproxy'>
>>> type(mappingproxy)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'mappingproxy' is not defined
```

## `coroutine`

Coroutine은 가장 일반적인 awaitable object이며, 다른 언어들의 future에 해당한다.
`CoroutineType` type은 `types` 모듈에 있는 alias이다.

## `reversed`

`__reversed__`를 구현하지 않는 sequence type들의 default reversed iterator type이다.
`reversed` type은 `builtins` 모듈에 있다.

## `generator`

`generator` type은 generator function이 return하는 generator iterator의 type이다.
정확한 terminology는 다음과 같다.

* Generator / Generator Function: `yield` expression이 있는 function
* Generator Iterator (= `generator` class): generator가 return하는 iterable/iterator

`GeneratorType` type은 `types` 모듈에 있는 alias이다.

## `async_generator`

`async_generator` type은 async generator function이 return하는 async generator iterator의 type이다.
`AsyncGeneratorType` type은 `types` 모듈에 있는 alias이다.

## Callable Types

파이썬에서 callable은 `__call__` 또는 Python 구현체에서 대응하는 slot을 구현하는 type이다.
즉, `foo()`는 `type(foo).__call__(foo)`이다.

만약 `__call__`이 slot wrapper가 아니라 function이라면 다시 attribute lookup하여,
`type(type(foo).__call__).__call__(type(foo).__call__, foo)`이다.

### `function`

파이썬에서 함수는 보통 **user-defined function**을 말하며,
`def` keyword로 정의할 수 있다.
`FunctionType` type은 `types` 모듈에 있는 alias이다.

### Lambda Types

파이썬은 `lambda` keyword를 통한 익명 함수를 지원한다.
즉, lambda 객체는 본질적으로는 함수이나, `__name__` attribute가 없다.

### `method`

파이썬에서 메소드는 class 또는 class instance와 함수의 relation으로 구현되어 있다.
`method` type의 인스턴스들은 `__self__` attribute가 클래스 또는 instance를,
`__func__` attribute가 underlying function을 참조한다.
메소드를 호출(`__call__`)했을 때 `__self__`를 맨 앞 인자로 추가하여 `__func__`를 호출한다.
`MethodType` type은 `types` 모듈에 있는 alias이다.

```py
>>> class Temp:
...     def hello(self): ...
...
>>> x = Temp()
>>> x.foo
<bound method Temp.foo of <__main__.Temp object at 0x7fa46001da30>>
>>> type(Temp.foo)
<class 'function'>
>>> type(x.foo)
<class 'method'>
```

클래스와 함께 메소드를 정의하면, 메소드를 정의할 때 작성한 함수 `hello`가 `Temp` type의 attribute가 된다. 이 부분(line 1~2)은 다음과 같다고 볼 수 있다.

```py
def hello(self): ...
class Foo:
    hello = hello
```

한편, line 4에서 `Temp` type의 instance `x`를 생성할 때, `x.hello`가 `x`와 `Foo.hello` 함수를 참조하는 `method` type의 instance를 참조하게 한다.
line 5~6를 보면 `x.foo`가 `Temp.foo` 함수를 `Temp` type의 instance에 bound시킨 method라고 친절하게 알려준다.

이렇게 되는 이유는 **함수가 descriptor이기 때문이다**!
함수는 class variable로서 lookup되었을 때 descriptor이므로 `__get__`을 호출하는데,
호출한 instance에 따라 return값이 달라진다.

* instance(e.g, `x.foo`): instance와 함수를 mapping한 method
* type(e.g, `Temp.foo`): 그냥 함수 그대로

따라서 line 7~10에서 `Temp.foo`는 함수 그대로이고, `x.foo`는 method임을 알 수 있다.

### `staticmethod`

`@staticmethod` decorator를 이용해 함수를 감싼 `staticmethod` object는 일반적인 `function`과는 다르게, `__get__` 호출 시 method를 만들지 않고 감싼 함수를 return한다.
따라서 staticmethod는 class에 bound된 일반 함수처럼 사용된다.
`staticmethod`는 `builtins` 모듈에 있다.

따라서 `Temp.foo`, `x.foo` 둘 다 원래 function이다.

### `classmethod`

`@classmethod` decorator를 이용해 함수를 감싼 `classmethod` object는 일반적인 `function`과는 `__get__` mechanisma이 다르다. 마찬가지로 호출한 instance에 따라 갈리는데,

* instance(e.g, `x.foo`): instance의 **type**과 함수를 mapping한 method
* type(e.g, `Temp.foo`): instance와 함수를 mapping한 method

따라서 `Foo.h()`와 `Foo().h()`는 동일하다.
`classmethod`는 `builtins` 모듈에 있다.

### `builtin_function_or_method`

`builtin_function_or_method` object는 `len`, `globals`, `list().append` 등등 Built-in function/method들의 type이다. 그러나 function/method와 크게 다르지 않다.
차이점은 객체 자체가 Python 구현체에 이미 생성되어 있다는 것이다.
구현체에서 두 객체의 type이 같은 이유는, function을 `__self__` 가 None인 method처럼 구현해놓았기 때문이다. 어차피 function body가 Python 구현체에 정의되어 있기 때문에 가능한 일이다.
`BuiltinFunctionType`, `BuiltinMethodType` type은 `types` 모듈에 있는 alias이다.

```py
>>> type(len)
<class 'builtin_function_or_method'>
>>> myList = list()
>>> type(myList.append)
<class 'builtin_function_or_method'>
>>> type(list.append)
<class 'method_descriptor'>  # Descriptor that returns built-in method
```

### `method_wrapper`

`method_wrapper`는 언어 구현체에 내장된 함수를 wrapping한 method(function)이다.
Built-in method와 거의 동일하지만, built-in method는 객체 자체가 Python 구현체에 내장되어 있는 반면 `method_wrapper`는 정의되어 있는 함수를 wrapping하여 새롭게 생성한 method이다.
`MethodWrapperType` type은 `types` 모듈에 있는 alias이다.

## Descriptor Types

Descriptor는 `__get__`, `__set__`, `__delete__`중 일부 또는 전부를 구현하는 type이다.
이는 attribute lookup에서 찾은 attribute가 class variable이고 descriptor라면 해당하는 descriptor method를 호출한 결과를 return한다.

### `property`

Property는 아마도 가장 많이 사용되는 user-defined descriptor일 것이다.
메소드 위에 `@property` decorator를 추가하면 해당 function으로 `property` object를 생성하여 class attribute에 mapping한다.
`property` type은 `builtins` 모듈에 있다.

### `getset_descriptor`

Internal descriptor type 중 하나로, `__get__`, `__set__`을 모두 구현하는 data descriptor이며, 호출되면 적절한 attribute를 return한다.
`GetSetDescriptorType` type은 `types` 모듈에 있는 alias이다.

### `member_descriptor`

Internal descriptor type 중 하나로, `__get__`, `__set__`을 모두 구현하는 data descriptor이며, 호출되면 적절한 attribute를 return한다.
`getset_descriptor`와 거의 동일하나 조금 더 다재다능하다.
`MemberDescriptorType` type은 `types` 모듈에 있는 alias이다.

### `method_descriptor`

Internal descriptor type 중 하나로, `__get__`을 구현하는 descriptor이며, 호출되면 호출한 객체가 instance일 때만 built-in method를 return한다.
즉, built-in method를 return한다는 점을 빼면 function과 거의 동일하다.
`MethodDescriptorType` type은 `types` 모듈에 있는 alias이다.

### `classmethod_descriptor`

Internal descriptor type 중 하나로, `__get__`을 구현하는 descriptor이며, 호출되면 built-in method를 return한다.
`method_descriptor`와의 차이점은 `classmethod`와 `function`의 차이와 동일하다.
`method_descriptor`는 function처럼 호출한 객체가 instance일 때만 해당 instance를 `__self__`로 하여 method를 생성하지만, `classmethod_descriptor`는 `classmethod`처럼 호출한 객체가 instance든 type이든 type을 `__self__`로 하여 built-in method를 return한다.
`ClassMethodDescriptorType`type은 `types` 모듈에 있는 alias이다.

### `wrapper_descriptor`

Internal descriptor type 중 하나로, `__get__`을 구현하는 descriptor이며, 호출되면 호출한 객체가 instance일 때만 method wrapper를 return한다.
`method_descriptor`와 거의 동일하나 method wrapper를 return한다는 점이 다르다.
`wrapper_descriptor` 객체들은 slot wrapper라고 불리는데,
그 이유는 return하는 method wrapper가 slot을 wrapping하여 생성한 method이기 때문이다.
`WrapperDescriptor` type은 `types` 모듈에 있는 alias이다.

## `slice`

Sequence Type의 indexing에 사용되는 또 다른 type으로, 범위를 나타내는 type이다.
range처럼 start, stop, step으로 구성된다.
대괄호 안에서는 `start:stop`로도 instance를 생성할 수 있다.
`slice` type은 `builtins` 모듈에 있다.

## `None`

파이썬에서는 C/C++나 Java의 `null`과 같은 역할을 하는 `None`이라는 이름의 object가 있다.
파이썬에는 `void` type이 없으며 리턴값을 명시하지 않으면 모두 `None` object를 리턴한다.
`None` object는 `builtins` 모듈에 있으며 불변 객체이다.

`NoneType` type은 `types` 모듈에 있는 alias이다.

## `NotImplemented`

`None`과 비슷한 느낌을 풍기는 `NotImplemented`라는 object도 있다.
이 object는 numerical method(e.g, `__add__`)나 rich comparison method(e.g., `__lt__`)가 정의되지 않는 경우(연산 불능)를 나타내는 return값이다.
예를 들어, list * int는 정의되어 있지만, int * list는 정의되지 않은 연산이므로 이 때 `int.__mul__(other: list)`은 `NotImplemented` 객체를 return한다.
`NotImplemented` object는 `builtins` 모듈에 있으며 불변 객체이다.

파이썬 내부 구현에서 산술연산자는 numerical method와 rich comparison method를 이용하는데,
먼저 left operand의 method를 호출한 후, 만약 `NotImplemented`를 return한다면 right operand의 reverse method(e.g., `__radd__`) 등 "관련된 다른 method"를 호출한다.
어떤 method들을 어떤 순서로 호출하는지는 연산자마다 정해져 있다.
만약 모든 method들이 `NotImplemented`를 return한다면 그제서야 `TypeError`를 발생시킨다.

`NotImplementedType` type은 `types` 모듈에 있는 alias이다.

## `Ellipsis`

파이썬에서 `...` 또는 `Ellipsis`의 사용법은 다음과 같다.

- `pass` 대신 사용
- type hinting에서 "any type"이란 뜻으로 사용
- `NumPy` 등에서 `:`과 같은 의미로 사용

`Ellipsis` object는 `builtins` 모듈에 있으며 불변 객체이다.

`EllipsisType` type은 `types` 모듈에 있는 alias이다.

## `object`

모든 클래스의 공통 조상인 class.
`object` type은 `builtins` 모듈에 있다.

## `type`

Type universe에 해당하는 class.
`type` type은 `builtins` 모듈에 있다.

## `super`

`super` object는 MRO를 따라 부모 class를 resolve하기 위한 객체로, Java의 `super` keyword를 모방하는 객체이다.
`super` type은 `builtins` 모듈에 있다.

## `module`

Module은 파이썬 코드의 기본 단위이다.
Module이라는 abstraction을 표현한 type이 `module` type이다.
`ModuleType` type은 `types` 모듈에 있는 alias이다.

# Built-in Functions

`open`, `exec` 등과 같이 systems programming에 관련된 내용은 제외했다.

## `abs(i)`

i의 절댓값을 구하는 함수. `i.__abs__()`를 호출한다.

## `aiter(it)`

Async iterable `it`의 `it.__aiter__()`를 호출하여 Async iterator를 return한다.

## `all(it)`

Iterable `it`의 element가 1개 이상 논리값이 거짓이면 False를, o/w True를 return한다.

## Awaitable `anext(it)`

Async iterator `it`의 `it.__anext__()`를 호출하여 coroutine을 return한다.

## `any(it)`

Iterable `it`의 element가 1개 이상 논리값이 참이면 True를, o/w False를 return한다.

## `ascii(obj)`

`repr(obj)`과 유사하나 non-ASCII를 `\U` 등으로 escape하여 return한다.

## `bin(obj)`

`obj.__index__()`를 호출하여 얻은 정수를 `0b`로 시작하는 binary string으로 변환한다. 

## Class `bool(obj)`

`bool` class를 호출하면 `obj`의 논리값을 return한다.
논리값은 다음 순서로 구한다.

1. `obj.__bool__()`을 구현하면 호출한 결과를 return한다.
2. `obj.__len__()`을 구현하면 `호출한 결과 > 0`을 return한다.
3. `True`를 return한다.

## `callable(obj)`

`obj`가 callable인지, 즉 `obj`의 type이 `__call__`을 구현하는지를 return한다.

## `chr(i)`

Unicode point `i`에 해당하는 string representation을 return한다.
예를 들어, `chr(97)`은 `'a'`, `chr(8364)`는 `'€'`. `ord()`의 inverse function이다.

유효한 범위는 0 ~ 1,114,111(0x10FFFF)이다.

## `delattr(obj, name)`

`obj.__delattr__(name)`를 호출하여 주어진 객체의 dictionary에서 해당 attribute를 삭제한다.
`del obj.name`과 같다.

## `dir(obj)`

`obj.__dir__()`를 호출하여 `obj`의 valid attribute list를 return한다.
이는 `obj`의 attribute dictionary 뿐만 아니라 access 가능한 모든 attribute이다.

## `divmod(a, b)`

`a.__divmod__(b)`를 호출하여 몫과 나머지를 return한다.

## `enumerate(it, start = 0)`

Iterable `it`의 각 element에 `start`부터 순서대로 번호를 붙인 tuple을 element로 가지는 iterator를 return한다.

## `filter(func, it)`

Iterable `it`의 element를 함수 `func`로 filtering한 iterator를 return한다.

## `format(value, format_spec='')`

`value`의 formatted representation, controlled by format_spec을 return한다.
f-string과 같다.

## `getattr(obj, name)`

`obj`의 attribute `name`을 lookup하는 함수. `obj.name`과 같다.

## `globals()`

Global namespace를 return하는 함수.

## `hasattr(obj, name)`

`getattr(obj, name)`의 성공 여부를 return하는 함수.

## `hash(obj)`

`obj.__hash__()`를 호출하여 hash-value를 구하는 함수.

## `hex(obj)`

`obj.__index__()`를 호출하여 얻은 정수를 `0x`로 시작하는 hex string으로 변환하는 함수.

## `id(obj)`

`obj`의 identity를 return하는 함수.

## `isinstance(obj, tp)`

`obj.__class__`가 `tp.__mro__`에 속하는지를 return하는 함수.
OOP 관점에서 `obj`가 `tp` 또는 그 superclass의 instance인지 여부.

## `issubclass(tp1, tp2)`

`tp2`가 `tp1.__mro__`에 속하는지를 return하는 함수.
OOP 관점에서 `tp1`이 `tp2`의 subclass인지 여부.

## `iter(it)`

Iterable `it`의 `it.__iter__()`를 호출하여 iterator를 return한다.

## `len(obj)`

`obj`의 `obj.__len__()`을 호출하여 길이를 return한다.

## `locals()`

현재 local scope의 namespace를 return한다.

## `map(func, it)`

Map-reduce의 Map. Iterable `it`의 각 element에 function `func`를 적용한 iterator.

## `max()`

Item들 중 최댓값을 return한다.

## `min()`

Item들 중 최솟값을 return한다.

## `next(it)`

Iterator `it`의 `it.__next__()`를 호출하여 다음 element를 return한다.

## `oct()`

`obj.__index__()`를 호출하여 얻은 정수를 `0o`로 시작하는 oct string으로 변환하는 함수.

## `ord(c)`

Unicode character `c`의 unicode point를 return하는 `chr()`의 역함수.

## `pow(base, exp)`

`base.__pow__(exp)`를 호출한다.

## `repr(obj)`

`obj.__repr__()`을 호출하여 객체의 printable representation을 담은 문자열을 return한다.
이 문자열의 내용이 interpreter에서 우리가 보는 것과 일치한다.
예를 들어 `s = 'hello'`면 s는 5글자 문자열이지만, interpreter는 `'hello'`로 보여주고,
`repr(s)`는 7글자 문자열 `'hello'`이다.

## `round(i)`

`i.__round__()`롤 호출하여 반올림한 결과를 return한다.

## `setattr(obj, name, value)`

`obj.__setattr__(name, value)`를 호출하여 object의 attribute를 set하는 함수.
`obj.name = value`와 같다.

## `sorted(it)`

Iterable `it`의 element를 정렬한 list를 return한다.

## `sum(it)`

Iterable `it`의 element를 모두 합한(`__add__`) 결과를 return한다.

## `vars(obj)`

`obj`의 writable attribute를 저장한 `obj.__dict__`를 return한다.

## `zip(*it)`

Iterable들의 각 element를 zip한 tuple을 element로 가지는 iterator를 return한다.
