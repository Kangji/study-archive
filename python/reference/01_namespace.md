# Namespace

Namespace는 language semantics의 일부로, 변수 이름과 객체(의 메모리 주소)의 mapping이다.
Scope별로 namespace가 정의되어 있으며, 변수를 참조할 때마다 namespace를 확인하게 된다.
서로 다른 namespace에 동일한 이름의 변수가 존재할 수 있으며, 이 때 가장 가까운 scope가 우선순위를 갖는다.

Python assignment는 namespace에 변수가 존재하지 않으면 새로 선언하기 때문에 일반적인 assignment로는 outer scope의 변수의 값을 변경할 수 없다. Namespace를 통해 직접 참조해야 한다.

```py
x = 3                         # Assignment: x -> addr(3) in global namespace
def func():
    x = 4                     # Assignment: x -> addr(4) in func namespace
    globals()['x'] = 1        # Assignment: x -> addr(1) in global namespace
    print(x)                  # Reference:  x -> addr(4) in func namespace
    print(globals()['x'])     # Reference:  x -> addr(1) in global namespace
print(x)                      # Reference:  x -> addr(1) in global namespace
```

## Namespace 출력하기

- `globals()`: global namespace를 보여준다.
- `locals()`: 현재 scope의 namespace를 보여준다.
