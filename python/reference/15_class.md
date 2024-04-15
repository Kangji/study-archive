# Class Creation

`class` 키워드를 이용하여 클래스를 정의할 때, class object가 생성되는 걸 우리는 안다.
그 과정은 instance를 생성하는 것보다 훨씬 복잡하다.

```py
class Member:
    def __repr__(self) -> str:
        return "<'Member' object>"
    
    def __set_name__(self, cls, name):
        """
        Set Name (instance) method of the class attribute is called while creating raw class object.

        :param `self`: `Member` instance which will be a class attribute
        :param `cls`: Raw class object whose class attribute is `self`
        :param `name`: Class attribute name of `self`
        """

        print('Member.__set_name__() is called.')
        print('Member.__set_name__() is called while creating raw class object.')
        print(f'Sets class attribute {name} of the class objct {cls}')

class Type(type):
    """Mocks `type` class."""

    def __repr__(self) -> str:
        """Print name of `Type`'s instance (self)."""
        return f"<class '{self.__name__}'>"

    def __prepare__(name, bases, **kwds):
        """
        Prepare (static)method of the metaclass is called to create namespace.
        By default, `type.__prepare__(name, bases)` is called.

        :param `name`: name of the class to create
        :param `bases`: bases of the class to create
        :param `**kwds`: provided keywords of the class to create
        """

        print('Type.__prepare__() is called.')
        print('Type.__prepare__() is called before creating a class whose metaclass is `Type`.')
        print(f'Creates namespace for the class {name} with bases {bases}, kwds {kwds}.')

        namespace = type.__prepare__(name, bases, **kwds)

        print(f'Created namespace {namespace} will be used while executing the body of class {name}.', end='\n\n')

        return namespace

    def __new__(mcs, name, bases, namespace, **kwds):
        """
        New (class)method of the metaclass is called to create raw class object.
        By default, `type.__new__(mcs, name, bases, namespace, **kwds)` is called.

        :param `mcs`: metaclass, which is `Type` by default
        :param `name`: name of the class to create
        :param `bases`: bases of the class to create
        :param `namespace`: namespace used to create the class
        :param `**kwds`: provided keywords of the class to create
        """

        print('Type.__new__() is called.')
        print('Type.__new__() is called to create a class object whose metaclass is `Type`.')
        print(f'Creates class {name} of metaclass {mcs} with bases {bases}, kwds {kwds} using namespace {namespace}.')

        obj = type.__new__(mcs, name, bases, namespace, **kwds)

        print(f'Created class {obj} is an instance of `Type`.', end='\n\n')

        return obj

    def __init__(cls, name, bases, namespace, **kwds):
        """
        Init (instance) method of the metaclass is called to initialize class object.
        By default, `type.__init__(cls, name, bases, namespace, **kwds) is called.

        :param `cls`: class object to initialize
        :param `name`: name of the class to initialize
        :param `bases`: bases of the class to initialize
        :param `namespace`: namespace used while creating the class
        :param `**kwds`: provided keywords of the class on create
        """

        print('Type.__init__() is called.')
        print('Type.__init__() is called to initialize a class object whose metaclass is `Type`.')
        print(f'Initializes class object {cls} whose name is {name} with bases {bases}, kwds {kwds} using namespace {namespace}.', end='\n\n')

        type.__init__(cls, name, bases, namespace, **kwds)

    def __call__(cls, *args, **kwds):
        """
        Call (instance) method of the metaclass is called when calling class object.
        By default, `type.__call__(cls, *args, **kwds) is called, which usually creates an instance of the class.

        :param `cls`: class object that is called
        :param `*args`: positional argument provided when calling class object
        :param `**kwds`: keyword argument provided when calling class object
        """

        print('Type.__call__() is called.')
        print('Type.__call__() is called when calling class object whose metaclass is Type.')
        print('Type.__call__() is usually called to create an instance of the class.')
        print(f'Calls class object {cls} with args {args} and kwds {kwds}.')

        instance = type.__call__(cls, *args, **kwds)

        print(f'Created instance {instance}', end='\n\n')

        return instance

print('=========Create class `Parent`=========', end='\n\n')

class Parent(metaclass=Type):
    print(f"Executes body of `Parent` with namespace provided by Type.__prepare__().")

    def __repr__(self) -> str:
        return "<'Parent' object>"

    def __init_subclass__(cls, **kwds) -> None:
        """
        Init Subclass (class)method of the base class is called before calling its own init method.

        :param `cls`: class object to initialize
        :param `**kwds`: provided keywords of the class on create
        """

        print('Parent.__init_subclass__() is called.')
        print('Parent.__init_subclass__() is called to initialize a class object whose base is `Parent`.')
        print(f'Initializes class object {cls} whose base is `Parent` with kwds {kwds}')
    
    print(f"Added '__module__', '__qualname__', '__repr__' and '__init_subclass__' to the namespace.", end='\n\n')

class WrongParent:
    def __repr__(self) -> str:
        return "<'WrongParent' object>"

    def __mro_entries__(self, bases):
        """
        MRO Entries (instance) method of the base is called during MRO resolution of the class to create.

        :param `self`: `WrongParent` object included in original `bases`.
        :param `bases`: Bases of the class to create.
        """

        print('WrongParent.__mro_entries__() is called.')
        print('WrongParent.__mro_entries__() is called during MRO resolution of the class to create.')
        print(f'Resolves MRO entry of {self} among bases {bases}.')

        mro = (WrongParent,)

        print(f'Replaced {self} into {mro}.', end='\n\n')

        return mro

print('=========Create class `Class`=========', end='\n\n')

class Class(WrongParent(), Parent, member=Member()):
    print(f"Executes body of `Class` with namespace provided by Type.__prepare__().")
    x = Member()

    def __repr__(self) -> str:
        return "<'Class' object>"

    print(f"Added '__module__', '__qualname__', 'x', '__repr__' and '__orig_bases__' to the namespace.", end='\n\n')
```

## Mechanism

1. 클래스의 base들 중에서 `type`의 instance가 아닌데 `__mro_entries__(self, bases)` 메소드가 있는 object이 있다면 해당 object를 `__mro_entries__`를 호출한 결과를 unpack하여 대체한다.
`self`는 문제가 된 object이고 `bases`는 주어진 클래스의 base들.
`__mro_entries__`는 반드시 tuple을 return해야 한다.
2. 정해진 MRO를 바탕으로 class의 (type)metaclass를 결정한다.
    1. Base가 없고 metaclass를 명시하지 않았다면 default로 `type`이다.
    2. `type`의 instance가 아닌 metaclass를 명시했다면 그대로 채용한다.
    3. 명시된 metaclass와 base들의 metaclass들 전부의 subtype인 type을 채용한다.
    4. 그런 type이 없으면 `TypeError`로 class를 생성에 실패한다.
3. 위에서 정해진 metaclass의 `__prepare__(name, bases, **kwds)`를 호출해서 namespace를 생성한다.
`name`은 만들 클래스의 이름, `bases`와 `kwds`는 클래스 정의(괄호 안)에 있는 것들이다.
단, kwds에서 `metaclass=`인 argument들은 전부 제외시킨다.
4. 만들어진 namespace에서 class body를 실행시킨다.
그 결과, namespace에 method의 원형 함수 등 class attribute들이 추가된다.
5. metaclass의 `__new__(mcs, name, bases, namespace, **kwds)`를 호출하여 클래스를 생성하고 `__class__`, `__mro__` 등 기본적인 것들을 셋팅한다.
`mcs`는 metaclass, `name`은 만들 클래스의 이름, `bases`와 `kwds`는 클래스 정의(괄호 안)의 것들, 그리고 `namespace`는 4의 결과이다.
이 때 하는 일은 먼저 namespace를 보고 class attribute를 setting하는데, 이 때 mapping되는 object의 `__set_name__(self, cls, name)`이 호출된다.
`self`는 attribute object, `cls`는 class object, 그리고 `name`은 attribute name이다.
그 다음은 각 base들에서 `__init_subclass__(cls, **kwds)`를 호출한다.
`cls`는 class object, `kwds`는 클래스 정의(괄호 안)에 있는 keyword argument들이다.
6. metaclass의 `__init__`을 호출하여 객체를 초기화한다.

## `Parent` Class

1. Base가 없으므로 skip
2. 2-3에서 Metaclass = `Type`으로 결정
3. Namespace = `{}`
4. Namespace = `{'__repr__': ..., '__init_subclass__': ..., ...}`
5. obj = `Type`의 instance, whose name is `Parent`

```txt
=========Create class `Parent`=========

Type.__prepare__() is called.
Type.__prepare__() is called before creating a class whose metaclass is `Type`.
Creates namespace for the class Parent with bases (), kwds {}.
Created namespace {} will be used while executing the body of class Parent.

Executes body of `Parent` with namespace provided by Type.__prepare__().
Added '__module__', '__qualname__', '__repr__' and '__init_subclass__' to the namespace.

Type.__new__() is called.
Type.__new__() is called to create a class object whose metaclass is `Type`.
Creates class Parent of metaclass <class '__main__.Type'> with bases (), kwds {} using namespace {'__module__': '__main__', '__qualname__': 'Parent', '__repr__': <function Parent.__repr__ at 0x1027da7a0>, '__init_subclass__': <function Parent.__init_subclass__ at 0x1027da840>}.
Created class <class 'Parent'> is an instance of `Type`.

Type.__init__() is called.
Type.__init__() is called to initialize a class object whose metaclass is `Type`.
Initializes class object <class 'Parent'> whose name is Parent with bases (), kwds {} using namespace {'__module__': '__main__', '__qualname__': 'Parent', '__repr__': <function Parent.__repr__ at 0x1027da7a0>, '__init_subclass__': <function Parent.__init_subclass__ at 0x1027da840>}.
```

## `Class` Class

1. `WrongParent()` 객체는 `type`의 instance가 아니므로 `__mro_entries__` 호출하여 `WrongParent` 클래스로 base가 바뀜
2. 2-3에서 Metaclass = `Type`으로 결정
3. Namespace = `{member: Member object}`
4. Namespace = `{member: Member object}`
5. obj = `Type`의 instance, whose name is `Class` + Member에서 `__set_name__`, Parent에서 `__init_subclass__` 호출됨

```txt
=========Create class `Class`=========

WrongParent.__mro_entries__() is called.
WrongParent.__mro_entries__() is called during MRO resolution of the class to create.
Resolves MRO entry of <'WrongParent' object> among bases (<'WrongParent' object>, <class 'Parent'>).
Replaced <'WrongParent' object> into (<class '__main__.WrongParent'>,).

Type.__prepare__() is called.
Type.__prepare__() is called before creating a class whose metaclass is `Type`.
Creates namespace for the class Class with bases (<class '__main__.WrongParent'>, <class 'Parent'>), kwds {'member': <'Member' object>}.
Created namespace {} will be used while executing the body of class Class.

Executes body of `Class` with namespace provided by Type.__prepare__().
Added '__module__', '__qualname__', 'x', '__repr__' and '__orig_bases__' to the namespace.

Type.__new__() is called.
Type.__new__() is called to create a class object whose metaclass is `Type`.
Creates class Class of metaclass <class '__main__.Type'> with bases (<class '__main__.WrongParent'>, <class 'Parent'>), kwds {'member': <'Member' object>} using namespace {'__module__': '__main__', '__qualname__': 'Class', 'x': <'Member' object>, '__repr__': <function Class.__repr__ at 0x1027daa20>, '__orig_bases__': (<'WrongParent' object>, <class 'Parent'>)}.
Member.__set_name__() is called.
Member.__set_name__() is called while creating raw class object.
Sets class attribute x of the class objct <class 'Class'>
Parent.__init_subclass__() is called.
Parent.__init_subclass__() is called to initialize a class object whose base is `Parent`.
Initializes class object <class 'Class'> whose base is `Parent` with kwds {'member': <'Member' object>}
Created class <class 'Class'> is an instance of `Type`.

Type.__init__() is called.
Type.__init__() is called to initialize a class object whose metaclass is `Type`.
Initializes class object <class 'Class'> whose name is Class with bases (<class '__main__.WrongParent'>, <class 'Parent'>), kwds {'member': <'Member' object>} using namespace {'__module__': '__main__', '__qualname__': 'Class', 'x': <'Member' object>, '__repr__': <function Class.__repr__ at 0x1027daa20>, '__orig_bases__': (<'WrongParent' object>, <class 'Parent'>)}.
```
