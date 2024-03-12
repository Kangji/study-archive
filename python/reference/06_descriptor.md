# Descriptor

Descriptor는 `__get__`, `__set__` 혹은 `__delete__` 중 적어도 한 개 이상을 구현하고 있는 class와 그 instance를 뜻한다.
좁은 의미로는 `__get__`을 구현하는 class와 instance를 뜻한다.
특히, `__get__`을 구현하고 `__set__`이나 `__delete__`를 구현하는 descriptor를 data descriptor라고 한다.

CPython에서 살펴보면 `tp_descr_get`과 `tp_descr_set`을 모두 구현하면 data descriptor,
`tp_descr_get`만 구현하면 그냥 descriptor.

```py
__get__(self, obj, objtype=None)
__set__(self, obj, value)
__delete__(self, obj)
```

Descriptor는 instance의 attribute에 접근할 때 찾은 object가 class variable인 descriptor이면 대응하는 slot을 호출한 결과를 return한다.

## Usage Example

### Dynamic Lookup

동적으로 값이 계속 바뀌는 attribute를 만들고 싶을 때 사용할 수 있다.

```py
class AttributeCount:
    def __get__(self, obj, objtype=None):
        return len(obj.__dict__)
    
    def __set__(self, obj, value):
        raise AttributeError

class MyClass:
    num_attr = AttributeCount()

a = MyClass()

>>> a.num_attr
0
>>> a.x = 3
>>> a.num_attr
1
```

line 15에서 `x`라는 attribute가 새로 생겨서 `a`의 `__dict__`에 추가되었다. 그래서 그 다음 줄에서 `num_attr` 값이 커졌다.

### Validator

`__set__`에서 유효성 검사 logic을 넣어줄 수 있다.

## Built-in Decorators

## Property Decorator

`property` object는 descriptor이다.

```py
class Foo:
    def __init__(self, x):
        self._x = x
    
    @property
    def x(self):
        return self._x

    @x.setter
    def x(self, x):
        self._x = x

a = Foo(3)

>>> a.x
3
>>> Foo.x
<property object at 0x7ff0981bed60>
```

이런 클래스가 있을 때, `@property`가 x를 decorating하므로 Foo.x는 기존 함수 `Foo.x(self)`를 wrapping하는 `property` type의 object가 되었다.
그리고 아래 코드는 `property` class를 python code로 표현해본 것이다. (실제로는 C로 구현되어 있다.)

```py
class property:
    def __init__(self, fget=None, fset=None, fdel=None, doc=None):
        self.fget = fget
        self.fset = fset
        self.fdel = fdel
        if doc is None and fget is not None:
            doc = fget.__doc__
        self.__doc__ = doc
    
    def __get__(self, obj, objtype=None):
        if obj is None:
            return self
        if self.fget is None:
            raise AttributeError
        return self.fget(obj)

    def __set__(self, obj, value):
        if self.fset is None:
            raise AttributeError
        self.fset(obj, value)
    
    def __delete__(self, obj):
        if self.fdel is None:
            raise AttributeError
        self.fdel(obj)
    
    def getter(self, fget):
        return type(self)(fget, self.fset, self.fdel, self.__doc__)
    
    def setter(self, fset):
        return type(self)(self.fget, fset, self.fdel, self.__doc__)
    
    def deleter(self, fdel):
        return type(self)(self.fget, self.fset, fdel, self.__doc__)
```

## Function

놀랍게도 파이썬 함수는 decorator이다. 왜냐? method를 return하기 때문.
class variable로 정의된 함수 `hello`를 instance를 통해 접근하려고 하면,
`hello`는 decorator이므로 `hello.__get__()`의 결과인 method를 return한다.
