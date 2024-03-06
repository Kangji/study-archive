# Hardware Description Language (HDL)

HDL은 "Hardware Description Language"의 약자로, 하드웨어의 동작을 소프트웨어적으로 기술하는데 사용되는 언어이다.
주로 하드웨어를 제작하기 전에 시뮬레이션을 통해 검증하기 위한 용도로 사용된다.

## HDL과 프로그래밍 언어와의 차이

HDL은 하드웨어의 동작을 기술하고 있기 때문에 **기본적으로 동시성을 가진다**.

<br>

```java
class ExampleClass {
    public static void main(String[] args) {
        final Scanner scanner = new Scanner(System.in);
        final int a1 = scanner.nextInt();
        final int a2 = scanner.nextInt();
        final int result = getAverage(a1, a2);
        System.out.println("The result of the operation is " + result);
    }

    private int getAverage(final int a1, final int a2) {
        return (a1 + a2) / 2;
    }
}
```

위의 간단한 자바 프로그램은 다음과 같은 순서로 동작한다.

1. **line 3~6**: 표준 입력을 받는 scanner 객체를 생성하고, 인자로 쓰일 정수 두 개를 입력받는다.
2. **line 7**: `getAverage(a1, a2)`를 호출하여 두 정수의 평균값을 구한다.
3. **line 8**: 결과를 표준 출력으로 뱉는다.

<br>

```c
module mkExampleModule();
  Reg#(Bit#(2)) counter1 <- mkReg(0);
  Reg#(Bit#(2)) counter2 <- mkReg(1);
  
  rule doSwap;
    counter1 <= counter2;
    counter2 <= counter1;
  endrule
endmodule
```

이번엔 위의 간단한 HDL 코드를 살펴보자. 코드에 대한 자세한 설명은 하지 않겠다. 위 코드는 매 clock cycle마다 `doSwap`이라는 동작을 하는 하드웨어를 기술한 코드이다.

* line 2~3: 2bit를 저장할 수 있는 레지스터 `counter1`과 `counter2`를 module 안에 정의했다.
* line 5~8: 매 clock cycle마다 이 하드웨어가 수행할 동작을 기술했다.
  * 이 때, line 6과 7은 **동시에 수행된다**.
  * 따라서 `doSwap`이 수행될 때마다 `counter1`과 `counter2`의 값이 서로 바뀌게 된다.

여기서 포인트는 line 6과 line 7은 각각 `counter1`과 `counter2`라는 하드웨어의 동작을 기술한 것인데, **서로 다른 두 하드웨어가 순차적으로 동작할 이유가 전혀 없다!**

이처럼 HDL을 짤 때는 항상 소프트웨어적으로 생각하는 것이 아닌, 하드웨어 관점에서의 접근이 필요하다.
