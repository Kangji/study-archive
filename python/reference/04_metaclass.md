# Metaclass

이전 글에서 말한 것 처럼 metaclass는 **type object**의 type을 나타내는 **type object**이다. (클래스의 클래스 / type의 type)

```py
class Temp(metaclass=MyMeta):
    pass

>>> type(Temp)
<class '__main__.MyMeta'>
```

## Construction of Instance

```py
class Temp:
    def __init__(self, *args, **kwargs):
        ...

x = Temp(x, y) # What are called here??
```

line 5는 `Temp.__class__.__call__(Temp, x, y)`와 같다.
따라서 metaclass는 클래스의 인스턴스 생성 과정에 관여한다.

그러면 `type` object의 내부 구현을 보자.
(동작 방식대로 코드를 쓴 것일 뿐, 실제 구현체와는 다를 수 있다.)

```py
class type:
    def __call__(cls, *args, **kwargs):
        obj = cls.__new__(cls, *args, **kwargs)
        cls.__init__(obj, *args, **kwargs)
        return obj
```

`type.__call__(Temp, x, y)`는 `Temp.__new__()`로 `Temp`의 instance를 만들고,
`Temp.__init__()`으로 인스턴스를 초기화한다.

따라서 metaclass의 `__call__` 부분이 class의 instance 생성 과정에 관여하게 된다.

## Example: Singleton Pattern

```py
class SingletonMeta(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in SingletonMeta._instances:
            SingletonMeta._instances[cls] = type.__call__(cls, *args, **kwargs)
        return SingletonMeta._instances[cls]

class MyClass(metaclass=SingletonMeta):
    pass

>>> a = MyClass()
>>> b = MyClass()
>>> id(a)
140468114365888
>>> id(b)
140468114365888
```

이렇게 `__call__`이 이미 호출된 적이 있으면 최초 호출시에 생성한 instance를 return하기 때문에,
`SingletonMeta`의 instance는 Singleton이 된다.
