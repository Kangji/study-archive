# Special Attributes and Methods

일반적으로 Python에서 Python 구현체에 접근할 수 없지만,
special method나 attribute라는 python 구현체의 object에 대한 python level abstraction을 제공하기도 한다.

# Special Attribute

몇몇 special attribute들을 살펴보자.

## `object.__dict__`

객체의 writable attribute를 저장한 dictionary를 참조한다.

## `object.__weakref__`

객체에 대한(to the object) weak reference를 list로 저장한다.
항상 존재하지는 않지만, 객체를 약하게 참조하기 위해서는 해당 attribute가 존재해야 한다.

## `object.__slots__`

객체의 predfined attribute name들을 sequence 형태로 저장한다.
`__slots__`가 존재하면 `__dict__`와 `__weakref__` attribute는 만들어지지 않는다.

`__slots__`는 기존의 attribute lookup 방식을 훨씬 더 빠르게 할 수 있게 해준다.
`__slots__`로 선언된 각 name마다 `member_descriptor` object를 만들어서,
해당 type의 instance가 attribute lookup을 할 때 다른 과정을 거치지 않고 바로 descriptor를 호출한다. 내부에서 각 instance별로 attribute가 참조하는 object를 관리한다.

또한 `__dict__`가 없기 때문에 다른 attribute는 추가할 수 없다.

## `object.__class__`

객체의 class object를 참조한다.

## `class.__bases__`

클래스의 부모 class들을 tuple 형태로 참조한다.

## `class.__mro__`

클래스의 MRO를 tuple 형태로 참조한다.

## `def.__name___`

Define하는 객체의 경우 namespace의 이름 말고 정의할 때 부여된 이름을 가지고 있다.
클래스, 함수, 메소드, descriptor, generator가 그렇다.

## `def.__module__`

객체가 정의된 module의 이름이다.

## `function.__globals__`

함수가 참조할 수 있는 global namespace를 참조한다.

## `function.__closure__`

함수의 free variable을 tuple of cell 형태로 참조한다.

## `function.__annotations__`

함수의 parameter와 return type annotation을 dictionary 형태로 참조한다.

## `function.__defaults__`

함수의 default parameter value를 dictionary 형태로 참조한다.

## `function.__kwdefaults__`

함수의 keyword-only parameter의 default value를 dictionary 형태로 참조한다.

## `method.__self__`

메소드의 self object를 참조한다.
참고로 built-in function도 `__self__` attribute를 가지지만 None이다.

## `method.__func__`

메소드의 original function을 참조한다.

## `module.__file__`

모듈이 정의된 파일의 경로이다.

# Special Methods

Special method들을 살펴보자. 이들 중 일부는 Python 구현체의 slot에 대응한다.

## `__new__(cls, *args, **kwargs)`

객체의 생성자이다. Python 구현체에서의 "객체"를 생성하고 type을 `cls`로 설정한다.
생성된 객체를 return한다.

## `__init__(self, *args, **kwargs)`

생성된 객체를 초기화한다. 주로 객체의 attribute들을 set한다.

## `__del__(self)`

객체의 소멸자이다. Reference count가 0이 되었을 때 GC에 의해 호출된다.
Cyclic reference가 있으면 Cyclic GC에 의해 detect되어 호출된다.

## `__repr__(self)`

객체의 "official" string representation을 return한다.
Interpreter에서 객체를 display할 때 사용된다.
또한, `__str__`을 구현하지 않는 경우의 fallback으로 쓰인다.

## `__str__(self)`

객체의 "informal" string representation을 return한다.
`print()`, `str()` 등에서 호출된다.

## `__format__(self, format_spec='')`

객체의 formatted representation을 return한다. f-string에서 호출된다.

## Rich Comparison Methods

객체의 대소비교시 호출된다. 예를 들어 `x < y`는 `x.__lt__(y)`를 호출한다.

* `__lt__(self, other)`
* `__le__(self, other)`
* `__eq__(self, other)`
* `__ne__(self, other)`
* `__gt__(self, other)`
* `__ge__(self, other)`

## `__hash__(self)`

객체의 hash값을 return한다. `hash()`에서 호출된다.
객체의 hash값은 객체의 equality 확인에 쓰인다.

## `__bool__(self)`

객체의 논리값을 return한다. `bool()`에서 호출된다.

## `__getattribute__(self, name)`

객체의 attribute lookup시 호출된다.

## `__getattr__(self, name)`

`__getattribute__`에 대한 fallback이며 같은 기능을 한다.

## `__setattr__(self, name, value)`

객체의 attribute set시 호출된다.

## `__delattr__(self, name)`

객체의 attribute를 `del`할 때 호출된다.

## `__dir__(self)`

객체의 유효한 attribute들을 return한다. 이는 attribute lookup에서 찾을 수 있는 것들이다.
`dir()`에서 호출된다.

## `__init_subclass__(cls)`

클래스를 상속하는 새로운 클래스를 선언할 때, 즉 class object가 생성될 때 호출된다.
이를 통해 자식 클래스의 초기화를 정의할 수 있다.

## `__set_name__(self, owner, name)`

객체가 `owner` 클래스의 name attribute(class variable)에 assign되었을 때 호출된다.
이 method는 클래스를 생성할 떄만 호출되고, 이후의 assign에서는 호출되지 않는다.

## `__mro_entries__(self, bases)`

클래스를 생성할 때 `type` 클래스의 인스턴스가 아닌 base가 있으면,
그리고 그 class에 `__mro_entries__` method가 있으면 호출한 결과로 base를 대체된다.
이 메소드는 반드시 `type` 클래스의 인스턴스로 구성된 tuple을 return해야 한다.

## `__prepare__(name, *bases, **kwds)`

클래스를 생성할 때 호출되어 클래스의 namespace를 만들어 return한다.
이 메소드는 클래스를 정의할 때 호출되기 때문에 metaclass에만 존재한다.
`name`은 생성하는 클래스 이름, `bases`는 base 클래스, `kwds`는 추가적인 keyword arguments들이다 (e.g, `metaclass=ABCMeta`)

## `__call__(self, *args, **kwargs)`

객체를 호출한다. `instance(*args, **kwargs)`에서 호출된다.

## Descriptor Methods

Descriptor type이 구현하는 method.
Attribute access시 찾은 class variable이 descriptor일 경우 호출된다.

* `__get__(self, instance)`: `__getattribute__(self, name)`에서 호출
* `__set__(self, instance, value)`: `__setattr__(self, name, value)`에서 호출
* `__delete__(self, instance)`: `__delattr__(self, name)`에서 호출

## Container Methods

### `__len__(self)`

Container object의 길이(element 수)를 return한다.

### `__getitem__(self, key)`

`object[key]`시 호출된다.

### `__setitem__(self, key, value)`

`object[key] = value`.

### `__delitem__(self, key)`

`del object[key]`.

### `__iter__(self)`

Iterable type이 구현하며 iterator를 return한다.

### `__aiter__(self)`

Asynchronous version.

### `__reversed__(self)`

Reversible type이 구현하며 reverse-iterator를 return한다.

### `__contains__(self, item)`

Container가 `item`을 가지고 있는지를 return한다.

## Numerical Methods

산술, 논리 연산시 호출하는 method들이다.
만약 두 피연산자간 연산을 지원하지 않는 경우 `NotImplemented`를 return한다.

* `__add__(self, other)`: +
* `__sub__(self, other)`: -
* `__mul__(self, other)`: *
* `__matmul__(self, other)`: @
* `__truediv__(self, other)`: /
* `__floordiv__(self, other)`: //
* `__mod__(self, other)`: %
* `__divmod__(self, other)`: `divmod()`
* `__pow__(self, other)`: `pow()`, **
* `__lshift__(self, other)`: <<
* `__rshift__(self, other)`: >>
* `__and__(self, other)`: &
* `__xor__(self, other)`: ^
* `__or__(self, other)`: |

다음은 위 method들의 reflected version이며, `other op self`를 의미한다.
이 method들은 original version이 `NotImplemented`를 return했을 때의 fallback이다.
즉, `x + y`는 `x.__add__(y)`를 호출하고, fallback으로 `y.__radd__(x)`를 호출한다.

* `__radd__(self, other)`: +
* `__rsub__(self, other)`: -
* `__rmul__(self, other)`: *
* `__rmatmul__(self, other)`: @
* `__rtruediv__(self, other)`: /
* `__rfloordiv__(self, other)`: //
* `__rmod__(self, other)`: %
* `__rdivmod__(self, other)`: `divmod()`
* `__rpow__(self, other)`: `pow()`, **
* `__rlshift__(self, other)`: <<
* `__rrshift__(self, other)`: >>
* `__rand__(self, other)`: &
* `__rxor__(self, other)`: ^
* `__ror__(self, other)`: |

다음은 augmented arithmetic assignment(e.g, +=)이다.

* `__iadd__(self, other)`: +=
* `__isub__(self, other)`: -=
* `__imul__(self, other)`: *=
* `__imatmul__(self, other)`: @=
* `__itruediv__(self, other)`: /=
* `__ifloordiv__(self, other)`: //=
* `__imod__(self, other)`: %=
* `__ipow__(self, other)`: **=
* `__ilshift__(self, other)`: <<=
* `__irshift__(self, other)`: >>=
* `__iand__(self, other)`: &=
* `__ixor__(self, other)`: ^=
* `__ior__(self, other)`: |=

다음은 단항 연산들이다.

* `__neg__(self)`: -
* `__pos__(self)`: +
* `__abs__(self)`: `abs()`
* `__invert__(self)`: ~

## Mathematical Methods

* `__round__(self)`: `round()`
* `__trunc__(self)`: `math.trunc()`
* `__floor__(self)`: `math.floor()`
* `__ceil__(self)`: `math.ceil()`

## Context Manager Methods

### `__enter__(self)`

Context(`with` statement)에 진입할 때 호출된다.

### `__exit__(self, exc_type, exc_value, traceback)`

Context에서 빠져나올 때 호출된다.
Parameter들은 발생한 exception에 대한 정보이며, 발생하지 않으면 다 None이다.

### Awaitable `__aenter__(self)`

Asynchronous context(`async with` statement)에 진입할 때 호출된다.

### Awaitable `__aexit__(self, exc_type, exc_value, traceback)`

Asynchronous context를 빠져나올 때 호출된다.
