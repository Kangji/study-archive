# Method Resolution Order

파이썬은 다중상속을 지원한다. 다중상속에서 항상 문제가 되는건 동일한 이름의 attribute가 존재할 때 어떤 attribute를 참조하는가이다.

클래스를 정의할 때 클래스 간 상속 관계를 그래프로 나타내면 반드시 DAG가 되어야 하고, DAG가 된다.
Method Resolution Order란 attribute lookup시 어떤 class의 attribute부터 볼 것인가 하는 순서이다.

```txt
mro = []
def mro(class, must_be_zero = true) is
    for base in class.bases, left to right
        if indegree(base) is zero then
            mro.insert(base)
            mro(base, must_be_zero = false)
        else if must_be_zero then
            return fail
        end
    end
end 
```

## super

`super` is a proxy object that delegates method calls to a parent or sibling class of type.

Java의 super와 같다고 생각할 수 있지만, Java는 다중상속이 불가능하며 `super` 키워드가 곧바로 부모 클래스를 나타내지만, 파이썬은 `super`라는 별개의 type이다.

```py
class super:
    @override
    def __init__(self, __t, __obj): ...
    @override
    def __init__(self, __t): ...
    @override
    def __init__(self): ...
```

`super()`는 메소드 안에서 실행되면 `super(__class__, self)`와 같다.
`super(__t, __obj)`는 `__obj`가 type일 경우 `issubclass(__obj, __t)`,
type이 아닐 경우 `isinstance(__obj, __t)`을 만족해야 한다.
이 때 `super` object는 `__self__`, `__self_class__`, `__thisclass__` attribute를 이용하여 MRO에 따라 부모의 method를 호출한다. `__self__`는 `__obj`, `__self_class__`는 `__obj.__class__`, `__thisclass__`는 `__t`를 참조한다.

이 type은 특수한 `__getattribute__`를 가지고 있어 `super().f()`처럼 attribute lookup을 할 경우 다음과 같이 수행된다.

1. `__self_class__.__mro__`에서 `__self_class__` 다음 type부터 차례로 attribute dictionary에서 attribute를 찾는다.
2. 만약 찾은 object가 descriptor이면 descriptor를 호출한 결과를 return한다.
3. 만약 찾은 object가 function이면 `__self__` object와 해당 function을 mapping한 method를 return한다.

3번 과정 때문에 `super().f()`를 호출하면 처음 호출할 때의 `self` 그대로 넘어간다.

`super(type)`은 unbound super object로, `__self__` 및 `__self_class__`가 None이다.
