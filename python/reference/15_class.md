# Class Creation

`class` 키워드를 이용하여 클래스를 정의할 때, class object가 생성되는 걸 우리는 안다.
그 과정은 instance를 생성하는 것보다 훨씬 복잡하다.

```py
class Type(type):
    def __repr__(self) -> str:
        return f"'{self.__name__}' class"

    def __prepare__(name, bases, **kwds):
        """staticmethod"""

        print('Type.prepare', name, bases, kwds, sep=', ')
        print('Type.prepare is called before creating a class whose metaclass is Type.', end='\n\n')

        namespace = type.__prepare__(name, bases, **kwds)
        namespace.update(kwds)

        print('Type.prepare(result)', namespace, sep=': ')
        print(
            'Type.prepare returns a namespace used while executing class body.', end='\n\n')

        return namespace

    def __new__(cls, name, bases, namespace, **kwds):
        """staticmethod"""

        print('Type.new', cls, name, bases, namespace, kwds, sep=', ')
        print('Type.new is called to create a class whose metaclass is Type.', end='\n\n')

        obj = type.__new__(cls, name, bases, namespace, **kwds)

        print('Type.new(result)', obj, sep=': ')
        print('Type.new returns new instance of Type.', end='\n\n')

        return obj

    def __call__(cls, *args, **kwargs):
        """instance method"""

        print('Type.call', cls, args, kwargs, sep=', ')
        print('Type.call is called when creating an instance of a class whose metaclass is Type.', end='\n\n')

        instance = type.__call__(cls, *args, **kwargs)

        print('Type.call(result)', instance, sep=': ')
        print('Type.call internally calls cls.__new__ and cls.__init__.', end='\n\n')

        return instance

class Parent(metaclass=Type):
    print('Parent.body is executed with namespace provided by Type.prepare', end='\n\n')

    def __repr__(self) -> str:
        return "'Parent' object"

    def __init_subclass__(cls, **kwargs) -> None:
        """classmethod"""

        print('Parent.init_subclass', cls, kwargs, sep=', ')
        print(
            'Parent.init_subclass is called when creating a subclass of Class.', end='\n\n')

class WrongParent:
    def __repr__(self) -> str:
        return "'WrongParent object"

    def __mro_entries__(self, bases):
        print('MRO Entries', self, bases)
        return (WrongParent,)

class Class(WrongParent(), Parent, member=Member()):
    member = member
    print('Class.body is executed with namespace provided by Type.prepare', end='\n\n')

    def __repr__(self) -> str:
        return "'Class' object"

    def __new__(*args, **kwargs):
        return object.__new__(*args, **kwargs)
```

## Mechanism

1. Base에서 `type`의 instance가 아닌데 `__mro_entries__`를 구현하는 type이 있다면 해당 type을 `__mro_entries__`를 호출한 결과를 unpack하여 대체한다.
`self`는 문제가 된 base이고 `bases`는 클래스 정의에 있는 bases.
`__mro_entries__`는 반드시 tuple을 return해야 한다.
2. 정해진 MRO를 바탕으로 class의 (type)metaclass를 결정한다.
    1. Base가 없고 metaclass를 명시하지 않았다면 default로 `type`이다.
    2. `type`의 instance가 아닌 metaclass를 명시했다면 그대로 채용한다.
    3. 명시된 metaclass와 base들의 metaclass들 전부의 subtype인 type을 채용한다.
    4. 그런 type이 없으면 `TypeError`로 class를 생성에 실패한다.
3. 위에서 정해진 metaclass의 `__prepare__`를 호출해서 namespace를 생성한다.
`name`은 만들 클래스의 이름, `bases`와 `kwds`는 클래스 정의에 있는 것들이다.
단, kwds에서 `metaclass=`인 argument들은 전부 제외시킨다.
4. 만들어진 namespace 안에서 class body를 실행시킨다.
이 때, namespace가 변할 수 있다.
5. metaclass의 `__new__`를 호출하여 객체를 생성하고 `__class__`, `__mro__` 등 기본적인 것들을 셋팅한다. 이 때 하는 일은 먼저 namespace를 보고 객체의 attribute를 mapping하는데 이 때 mapping되는 object의 `__set_name__`이 호출된다. 그 다음은 각 base들에서 `__init_subclass__`를 호출한다.
6. metaclass의 `__init__`을 호출하여 객체를 초기화한다.

## `Parent` Class

1. Base가 없으므로 skip
2. 2-3에서 Metaclass = `Type`으로 결정
3. Namespace = `{}`
4. Namespace = `{'__repr__': ..., '__init_subclass__': ..., ...}`
5. obj = `Type`의 instance, whose name is `Parent`

## `Class` Class

1. `WrongParent()` 객체는 `type`의 instance가 아니므로 `__mro_entries__` 호출하여 `WrongParent` 클래스로 base가 바뀜
2. 2-3에서 Metaclass = `Type`으로 결정
3. Namespace = `{member: Member object}`
4. Namespace = `{member: Member object}`
5. obj = `Type`의 instance, whose name is `Class` + Member에서 `__set_name__`, Parent에서 `__init_subclass__` 호출됨
