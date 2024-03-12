# Closure

Closure는 자신을 둘러싼 scope의 state를 저장하는 함수이다.
근원은 lambda calculus에서 free variable을 가지는 function이다.
한편, Python에서는 global scope는 어디에서나 접근할 수 있으므로,
closure가 의미를 갖기 위해서는 중첩 함수로 정의되야 하고, 중간 scope의 변수를 capture해야 한다.

```py
def memoization(func):
    cache = {}
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper
```

여기서 보면 wrapper 함수는 중간 scope에 있는 cache와 func를 free variable로 capture한다.
