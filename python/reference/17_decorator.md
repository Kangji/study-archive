# Decorator

Python에 `@property`같은 decorator는 Java의 annotation과는 다르다.

```py
@myDeco
def myFunc(*args, **kwargs): ...

# is (almost) equal to

def myFunc(*args, **kwargs): ...
myFunc = myDeco(myFunc)
```

따라서 decorator는 callable이기만 하면 된다.

## Example

```python
class TestFailException(Exception):
    pass

class RunWithTest:
    def __init__(self, expected_result):
        self._expected = expected_result
    
    def __call__(self, func):
        def wrapper(*args, **kwargs):
            ret = func(*args, **kwargs)
            if ret != self._expected:
                raise TestFailException
            return ret
        return wrapper

@RunWithTest(7)
def myFunc(x, y):
    return x + y
```

`RunWithTest(7)` decorator를 적용시킨 `myFunc`는 argument로 주어진 x와 y의 합이 7이 아니면 `TestFailException`을 발생시킨다.

이처럼 `RunWithTest` decorator는 어떤 함수의 기대값을 assert하는 효과를 지닌다.
