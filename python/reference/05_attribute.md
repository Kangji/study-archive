# Attribute

What happens when we get or set an attribute of a Python object?
This question is not as simple as it may seem at first.

이번 글에서는 `instance.attr`처럼 attribute에 접근할 때 Python 구현체에서 실제로 어떤 일이 일어나는지 CPython을 예시로 살펴볼 것이다.

Python level에서 `PyClass` 클래스의 instance `py_instance`의 attribute `attr`에 접근(get/set)하는 과정을 살펴볼 것이다.
CPython에서 `PyClass`는 `PyTypeObject *class`, `py_instance`는 `PyObject *instance`이다.

## Getting an Attribute

CPython은 `instance`의 type인 `class`의 slot 중 `tp_getattro`, `tp_getattr` 순으로 slot을 호출하여 `instance`의 attribute를 구한다.
`tp_getattro`와 `tp_getattr`의 차이는 `tp_getattro`가 Python string을 인자로 받는다는 차이점 뿐이며, `tp_getattr`은 더 이상 사용되지 않으니 고려하지 않겠다.
`py_instance.attr`는 `class->tp_getattro(instance, PyStr("attr"))`를 호출한다.

또한 `PyClass`에 `__getattribute__`, `__getattr__` special method가 있다면 CPython은 이들을 `tp_getattro`와 [mapping](#custom-attribute-get)한다.

### Generic Get Attribute (Instance)

`instance`가 class object가 아니면 `tp_getattro`는 `PyObject_GenericGetAttr()`를 참조한다. 이 함수의 구현은 다음과 같다.

1. MRO(Method Resolution Order)대로 `class` 및 `class`의 base들의 attribute dictionary에서 `attr`(class variable)을 찾는다.
만약 찾은 variable이 **data** descriptor이면 이 variable의 `tp_descr_get` slot을 호출한 결과를 return한다.
만약 data descriptor가 아니면 step 2가 우선순위이므로 기억만 해두고 3으로 넘어간다.
2. `instance`의 attribute dictionary에서 `attr`(instance variable)을 찾았다면 return한다.
3. 1에서 찾은 결과를 return한다.
만약 descriptor라면 `tp_descr_get` slot을 호출한 결과를 return한다.

### Type Get Attribute (Class)

`instance`가 class object인 경우, 즉 `PyTypeObject *instance`일 경우 `tp_getattro`는 `type_getattro()`를 참조한다.
이 함수는 `PyObject_GenericGetAttr`과 큰 흐름은 같지만, step 2가 다르다.

2. MRO대로 `instance` 및 `instance`의 base들의 attribute dictionary에서 `attr`(class variable)을 찾아서 return한다.
단, 찾은 variable이 찾은 variable이 descriptor라면 `tp_descr_get` slot을 호출한 결과를 return한다.

이 차이는 찾은 attribute가 descriptor일 때,
instance variable이면 `tp_descr_get`을 호출하지 않는다는 점과,
base가 존재하면 MRO대로 base도 살펴본다는 규칙으로 요약할 수 있다.

### Custom Get Attribute

만약 `__getattribute__` 또는 `__getattr__`을 구현하는 Python 클래스라면 CPython은 해당 class object의 `tp_getattro` slot을 `slot_tp_getattr_hook()`를 참조시킨다.
이 함수는 `__getattribute__` python function과 `__getattr__` python function을 차례로 실행시킨다.
만약 `__getattribute__`만 구현한다면 `slot_tp_getattro`를 참조시키는데,
이 함수는 `slot_tp_getattr_hook`과 비교하여 `__getattr__`을 실행하는 부분이 빠져있다.

## Setting an Attribute

CPython은 `instance`의 type인 `class`의 slot 중 `tp_setattro`, `tp_setattr` 순으로 slot을 호출하여 `instance`의 attribute를 set한다.
마찬가지로 `tp_setattr`은 더이상 사용되지 않는다.
`py_instance.attr = value`는 `class->tp_setattro(instance, PyStr("attr"), value)`를 호출한다.

또한 `PyClass`에 `__setattr__`, `__delattr__` special method가 있다면 CPython은 이들을 `tp_setattro`와 [mapping](#custom-attribute-set)한다.

### Generic Set Attribute

`instance`가 class object가 아니면 `tp_setattro`는 `PyObject_GenericSetAttr()`를 참조한다. 이 함수의 구현은 다음과 같다.

1. MRO대로 `class` 및 `class`의 base들의 attribute dictionary에서 `attr`(class variable)을 찾는다.
만약 찾은 variable이 `tp_descr_set`을 구현하는 descriptor라면 이 slot을 호출한 결과를 return한다. 그렇지 않다면 step 2로 넘어간다.
2. `instance`의 attribute dictionary에 `attr`을 추가하거나 기존 `attr`을 update한다.

### Type Set Attribute

`instance`가 class object인 경우 `tp_setattro`는 `type_setattro()`를 참조한다.
이 함수는 내부적으로 `PyObject_GenericSetAttr`을 호출하지만,
그 외에도 이 class가 statically defined type(e.g, built-in type)이라면 익셉션을 발생시키고, attribute가 special method라면 대응하는 slot을 update한다.

Statically defined type은 Python 구현체를 실행시킬 때 이미 내장되어 있는 type object다.
이런 type들은 기본적으로 불변이다.

### Custom Attribute Set

만약 `__setattr__` 또는 `__delattr__`을 구현하는 Python 클래스라면 CPython은 해당 class object의 `tp_setattro` slot을 `slot_tp_setattro()`를 참조시킨다.
이 함수는 value가 NULL인지 아닌지에 따라 `__setattr__` 또는 `__delattr__` python function을 실행시킨다.
