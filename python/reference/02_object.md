# Object Abstraction

파이썬에서는 모든 것이 객체라는 말을 들어봤을 것이다.
정확히는 객체가 Python 구현체가 python을 구현하는 기본 abstraction이다.
예를 들면, 함수, 클래스(심지어는 primitive type까지), 파이썬 코드 등이 전부 객체로 표현된다.
앞으로 Python Spec을 따질 때 구현체 수준까지 내려간다면 CPython을 살펴볼 것이다.

## Components of Objects

파이썬의 모든 객체는 **Identity**, **Type**, **Value**를 가진다.
만약 객체가 type일 경우 0개 이상의 **Base**를 가진다.

### Identity

Identity는 모든 객체가 가지는 고유한 불변의 정수이다.
그리고 `is` operator는 두 객체의 identity를 비교한다.
한편, CPython에서는 인스턴스의 메모리 주소를 identity로 사용한다.

`id()` 내장 함수가 객체의 identity를 return한다.

### Type

모든 객체는 불변 속성인 Type을 가진다.
객체의 type은 객체의 value와 operation을 결정한다.

`type()` 함수가 객체의 type을 return한다.
참고로, 객체의 type 또한 객체로 구현되며, 이를 type object라고 한다.

Python 구현체에 내장된 객체의 경우 대부분 Python level에서는 type이 존재하지 않을 수 있다.
Python level에서 존재하지 않는다는 것은 Python level에서 mapping된 이름이 없다는 것이지, type은 항상 존재한다.
이런 객체들은 `None` 객체 등 특수한 객체여서 Python level에서 type이 필요 없거나,
함수 등 객체를 생성하는 방식이 따로 있는 객체들이다.

또한, 파이썬의 클래스는 구현체에서 정확히 type이다. 따라서 class는 python level, type은 구현체 level에서의 명칭일 뿐 본질적으로 동일하다. 따라서 많은 경우 두 용어를 함께 사용한다.
파이썬에서 모든 type은 class이기 때문에 모든 객체는 instance임을 알 수 있다.

### Value

모든 객체는 가변(mutable) 혹은 불변(immutable) 속성인 Value(값)를 가진다.
한편, 가변 객체를 referencing하는 불변 객체의 경우 reference만 바뀌지 않을 뿐 referenced object 자체는 가변이다. 이런 성질을 changeable이라고 한다.

앞서 type이 객체가 가질 수 있는 value를 정의한다고 했는데, 객체의 불변성 또한 type에 따라 결정된다.

### Base

Base는 OOP의 상속 개념을 구현한 것이다. 파이썬에서 상속은 클래스끼리의 base라는 관계로 나타내어진다.
모든 type은 0개 이상의 base를 가질 수 있어서 다중상속이 가능하다.

Base는 class의 성질이기 때문에 class들만 base를 가진다.

```py
>>> x = 3
>>> x.__base__
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'int' object has no attribute '__base__'.
>>> int.__base__
<class 'object'>
>>> int.__bases__
(<class 'object'>,)
```

## Literals

모든 것은 객체로 표현되기 때문에 정수, 문자열 등의 literal들도 객체이다.
예를 들어, 정수 literal `2`는 `int` class의 인스턴스이며,
그 증거로 정수 literal `2`도 identity 값을 가진다.

Literal들은 구현체에 내장되어 있지 않고 처음 그 값이 참조되었을 때 인스턴스를 생성한다.
무한히 많은 literal들이 내장되어 있을 수는 없기 때문에 자명하다.
뿐만 아니라 몇몇 케이스를 제외하면 singleton이다.

```py
>>> id(2)
4355941704
>>> id(2)
4355941704
>>> x = 2
>>> id(x)
4355941704
>>> id(3)
4355941780 # different!
```

Numeric type은 부분적으로 singleton인데, 이유는 잘 모르겠다.
그러나 literal이 존재하는 type들은 모두(아마?) instance가 불변 객체이기 때문에,
singleton인지는 별로 중요하지 않다.

```py
>>> id(1.0)
4316292912
>>> # x op y style float with value 1.0 are identical
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

## Class Name

한 가지 중요한 사실은 class name은 class의 일부가 아니다.
이는 namespace에 저장된 class와의 mapping일 뿐이다.
따라서 자명하게도 여러 namespace에 저장된 여러 이름들이 같은 객체를 참조할 수 있다.

```py
# This is valid
class Foo: ...
Bar = Foo

x = Bar()

>> type(x)
<class '__main__.Foo'>
>> globals()['Foo']
<class '__main__.Foo'>
```

## Object Class

Java에서 모든 클래스의 공통조상인 `Object` 클래스가 존재하듯이,
파이썬도 모든 클래스의 공통조상인 `object` 클래스가 존재한다.

`object` 클래스는 base가 없는 유일한 <u>클래스</u>이다.
정확히 표현하면 base가 있긴 한데, empty tuple이다.

파이썬에서 `object`를 제외한 모든 클래스는 다른 클래스를 base로 갖기 때문에 귀납적으로 `object`를 제외한 모든 클래스는 `object`를 조상으로 갖는다.

## `PyObject` in C API

CPython에서 type이 아닌 모든 객체는 `PyObject` 구조체 type 변수이다.
이 구조체는 객체의 execution을 위해 필요한 여러 data 또는 function pointer들을 member로 갖는다.
