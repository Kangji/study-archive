# Memory Ordering

When dealing with multiple shared objects, re-ordering of memory instructions(Out-of-Order Execution) will lead to inconsistency.

## Example

```cpp
int thread(std::atomic<int> *x, std::atomic<int> *y) {
    y->store(1);
    return x->load();
}

int main() {
    std::atomic<int> a(0);
    std::atomic<int> b(0);

    thread1 = std::thread(thread, &a, &b);
    thread2 = std::thread(thread, &b, &a);
}
```

Thread 1과 2의 return값은 상식적으로 (0, 1), (1, 0), (1, 1)이 가능할 것이다.

```cpp
// (1, 0)
a->store(1);
return b->load();
                    b->store(1);
                    return a->load();

// (1, 1)
a->store(1);
                    b->store(1);
return b->load();
                    return a->load();

// (0, 1)
                    b->store(1);
                    return a->load();
a->store(1);
return b->load();
```

그러나 re-ordering이 가능하기 때문에 아래와 같은 실행 순서도 가능하며 이 경우 (0, 0)이 나온다.

```cpp
// (0, 0)
                    return a->load();
return b->load();
                    b->store(1);
a->store(1);
```

참고로 intel CPU는 서로 다른 memory에 대한 쓰기-읽기만 재배치가 가능하다.

## Memory Barrier

Memory barrier를 통해 instruction reordering을 방지할 수 있다.
마치 instruction이 넘어가지 못하도록 막는 barrier같은 역할.

### Relaxed

모든 reordering을 허용

### Release

해당 (write) instruction 앞에 오는 모든 memory instruction의 reordering을 금지

### Acquire

해당 (read) instruction 뒤에 오는 모든 memory instruction의 reordering을 금지

### Synchronized With

같은 변수에 대한 release, acquire barrier를 통해 두 thread를 동기화시킬 수 있다.

```c
// other operations
v->store(true, release);
                            while (v->load(acquire)); // synchronized
                            // other operations
```

### AcqRel

Acquire + Release. Read, write를 동시에 하는 memory instruction에 사용 가능.

### SeqCst

Sequential Consistency를 보장하는 memory barrier.

Sequential Consistency는 모든 memory instruction들의 관측 순서에 대한 global consistency이다.
즉, 모든 thread에서 동일한 순서로 관측되어야 한다.

Acquire, Release는 한 thread 내에서 memory instruction의 실행(관측) 순서에 대한 consistency이다.
그러나 서로 다른 thread에서 일어나는 memory instruction의 순서에 대한 보장은 없다.

예를 들어서 두 thread에서 각각 write operation을 수행할 때 한 thread에서 일어난 write가 다른 thread에 보이는 시점은 다를 수 있다.
따라서 한 thread에서 보면 write A가 write B보다 먼저 관측될 수 있고, 동시에 다른 thread에서 보면 write B가 write A보다 먼저 관측될 수 있다.
물론 modification order 때문에 이 두 write operation은 서로 다른 variable에 대한 write여야 한다.

그러나 SeqCst는 모든 memory instruction들의 관측 순서가 모든 thread에서 동일해야 하므로 이런 일은 발생할 수 없다.
이를 보장하기 위해 SeqCst는 각 memory instruction이 globally visible할 때까지 다음 SeqCst instruction을 delay시킨다.

```cpp
std::atomic<bool> x(false);
std::atomic<bool> y(false);
std::atomic<int> z(0);

void write_x() { x.store(true, release); }
void write_y() { y.store(true, release); }
void read_x_then_y() {
    while (!x.load(acquire)); // synchronize with x
    if (y.load(acquire)) ++z;
}
void read_y_then_x() {
    while (!y.load(acquire)); // synchronize with y
    if (x.load(acquire)) ++z;
}

std::thread thread1(write_x);
std::thread thread2(write_y);
std::thread thread3(read_x_then_y);
std::thread thread4(read_y_then_x);

// z = ?
join_all(thread1, thread2, thread3, thread4);
```

만약 sequential consistency를 보장한다면 write x와 write y의 관측 순서가 thread 3과 4에서 모두 x -> y이거나 모두 y -> x일 것이고, 따라서 둘 중 적어도 하나는 z의 값을 증가시킬 것이다.
그러나 sequential consistency 없이는 thread 3에서는 write x(t1) -> read x(t3) -> read y(t3) = False -> write y(t2), thread 4에서는 write y(t2) -> read y(t4) -> read x(t4) = False -> write x(t1)일 수 있다.
