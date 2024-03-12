# Abstract Base Class

Python에서 bstract class를 구현한 방법. `abc` 모듈에 있다.

## Class `ABC`

`ABC` class를 상속하는 type은 추상 클래스처럼 사용할 수 있다.
해당 클래스의 메소드 중 `@abstractmethod`가 달린 method는 추상 메소드가 되며,
클래스의 추상 메소드 list에 추가된다.
이 클래스는 추상 메소드가 단 하나라도 존재하면 instance를 생성할 때 익셉션이 발생한다.

## Metaclass `ABCMeta`

이런 동작들은 `ABCMeta`라는 metaclass에 구현되어 있다.
클래스를 생성할 때 namespace에 `__abstractmethods__` attribute를 만들고,
`@abstractmethod` decorator에 의해 `abstractmethod`가 호출되면 해당 method를 추가한다.
