# Monitor

Object with a set of monitor procedures => one thread may be active at a time
* Motivation: Concurrent Programming + Object-Oriented Programming
* Language implementation of mutex => Compiler automatically inserts lock and unlock operations upon entry and exit of monitor procedures

```java
class Account {
    int balance;
    public synchronized void deposit() {
        balance++;
    }

    public synchronized void withdraw() {
        balance--;
    }
}
```
