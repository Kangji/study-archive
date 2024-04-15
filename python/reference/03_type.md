# Type Object

파이썬의 클래스는 구현체에서 정확히 type이다. 따라서 class는 python level, type은 구현체 level에서의 명칭일 뿐 본질적으로 동일하다. 따라서 많은 경우 두 용어를 함께 사용한다.
파이썬에서 모든 type은 class이기 때문에 모든 객체는 instance임을 알 수 있다.
즉, class A의 인스턴스의 type은 A이고, A의 type은 A가 아니다 (나중에 다룰 예정).

한편, class(type) 또한 "모든 것"에 포함되므로 객체이며 어떤 class의 인스턴스이다.
Class(type)에 해당하는 객체를 **class(type) object**라고 한다.

Class object의 type을 알아보기 위해 `type` class를 살펴봐야 한다.

## Expressing Objects and Classes

표현이 혼동하기 쉬워 정리하고 넘어간다.

* a: Identifier a가 참조하는 object를 지칭
* A object: A가 클래스 이름일 경우 A의 instance를 지칭, o/w, A가 참조하는 object를 지칭
* A type(class): A는 클래스 이름이고, class object를 지칭

## `type` class

모든 클래스의 base class로 `object` class가 있듯이 type의 type을 나타내는 default type으로 `type` class가 존재한다.

물론, `type` class도 object이므로 type을 가지는데, `type` class의 type은 자기 자신이다.
또한 `object` class의 type도 `type` class이다.

```py
>>> type(int)
<class 'type'>
>>> type(float)
<class 'type'>
>>> type(type)
<class 'type'>
>>> type(object)
<class 'type'>
```

## Metaclass

`type` class는 type theory의 type universe 개념이다.
그러나 모든 클래스가 `type` class를 type으로 가지지는 않는다.
`type` class를 포함하여, class의 class들을 통틀어 **metaclass**라고 부른다.

한편, 자기 자신을 type으로 가지는 클래스는 `type`이 유일하다.

## Fun Stuff

```py
>>> isinstance(type, type)
True
>>> isinstance(type, object)
True
>>> isinstance(object, type)
True
>>> isinstance(object, object)
True
>>> type is object
False
```

`isinstance(arg1, arg2)` 함수는 `arg1`의 type이 `arg2` 뿐만 아니라 `arg2`의 base인 경우도 고려한다.

- `isinstance(object, object)`는 `object`의 type인 `type` class가 `object`를 base로 갖기 때문에 참이다.
- `isinstance(object, type)`은 `object`의 type이 `type` class이기 때문에 참이다.
- `isinstance(type, object)`는 `type`의 type인 `type` class가 `object`를 base로 갖기 때문에 참이다.
- `isinstance(type, type)`은 `type`의 type이 `type` class이기 때문에 참이다.

## `PyTypeObject` in C API (CPython)

The C structure of the objects used to describe built-in types.
이 구조체는 `tp_repr`, `tp_hash` 등 execution을 위해 필요한 data 또는 function pointer들을 필드로 가진다. 이 필드들을 slot이라고 한다.
참고로 slot은 CPython 개념이 아니라 python level에서 정의된 언어 구현체의 interface에 속한다.

특히 Slot들 중 일부는 special attribute / method의 default가 된다.
예를 들어, `tp_repr`이 참조하는 C 함수는 `__repr__` method의 원형이다.

### Slot Wwrapper & Special Method

Python 구현체에서 어떤 type이 special method에 대응하는 slot을 구현한다면,
Python에서는 해당 special method는 그 slot implementation을 감싸는 객체이다.
이런 object를 slot wrapper라고 한다.

또한, Python에서 어떤 class가 special method를 구현한다면 (special method를 attribute로 가질 때) Python 구현체에서 해당 type의 해당 slot은 그 method object를 호출하는 default implementation이다.
