- [쓰레드 구현 방법](#쓰레드 구현 방법)

<br/>

# 쓰레드

## 쓰레드 구현 방법
`Thread` 클래스를 상속받는 방법, `Runnable` 인터페이스를 구현하는 방법이 있다

어느 쪽을 선택해도 별 차이는 없지만 Thread 클래스를 상속받으면 다른 클래스를 상속받을 수 없기 때문에, Runnable 인터페이스를 구현하는 방법이 **일반적**이다.

쓰레드를 구현한다는 것은 어떤 방법을 선택하든지, 그저 쓰레드를 통해 작업하고자 하는 내용으로 `run()` 메서드의 내용을 채우는 것일 뿐이다.

<br/>

### `Thread` 클래스를 상속받는 방법
```java
class MyThread extends Thread {
    // Thread 클래스의 run()을 오버라이딩
    public void run() { /* 작업 내용 */ }
}
```

**쓰레드 인스턴스 생성**

```java
Thread thread = new Thread(new MyThread());
```

<br/>

### `Runnable` 인터페이스를 구현
```java
class MyThread implements Runnable {
    // Runnable 인터페이스의 run()을 구현
    public void run() { /* 작업 내용 */ }
}
```

**쓰레드 인스턴스 생성**

```java
Runnable runnable = new MyThread();
Thread thread = new Thread(runnable);
```

Runnable 인터페이스를 구현한 경우, Runnable 인터페이스를 구현한 클래스의 인스턴스를 Thread 클래스의 생성자 매개변수로 제공해야 한다.
```java
// Thread 클래스
public class Thread {
    private Runnable r;

    public Thread(Runnable r) {
        this.r = r;
    }

    ...
}
```

<br/>

### 쓰레드 실행 - `start()`

`start()`를 호출해야 쓰레드가 실행된다. 호출되었다고 해서 바로 실행되는 것은 아니고, 일단 실행대기 상테에 있다가 자신의 차례가 되어야 실행된다.
쓰레드의 실행순서는 OS의 스케줄러가 작성한 스케줄에 의해 결정된다.

하나의 쓰레드에 대해 `start()`는 한 번만 호출될 수 있다. 쓰레드의 작업을 한 번 더 수행해야 한다면 새로운 쓰레드를 생성한 다음 실행해야 한다. 만약 두 번 이상 호출하면 `IllegalThreadStateException`이 발생한다.

<br/>

---

<br/>

출처 
- Java의 정석(남궁 성)