# JVM (Java Virtual Machine)

Java를 실행하기 위한 가상 머신[( Virtual Machine )](../etc/virtual_machine.md). 즉, 컴퓨터에서 Java를 실행하기 위해 소프트웨어로 구현된 가상의 하드웨어로 컴퓨터와 거의 동일한 기능을 수행할 수 있는 컴퓨터의 가상화된 인스턴스이다.

Java로 작성된 애플리케이션은 모두 이 가상 컴퓨터(JVM)에서만 실행되기 때문에 Java 애플리케이션이 실행되기 위해서는 반드시 JVM이 필요하다.

<br/>

<p align="center">
    <img src="../image/jvm.png"  width="220" height="auto">
</p>

<br/>

Java 애플리케이션은 JVM을 거쳐 OS → 하드웨어로 전달된다. 만약, Java 애플리케이션과 OS 사이에 JVM이 없다면 다른 OS에서 Java 애플리케이션을 실행시키기 위해서는 애플리케이션을 그 OS에 맞게 변경해야한다. 하지만 Java 애플리케이션은 JVM 하고만 상호작용을 하면 된다. 다른 OS에서 Java 애플리케이션을 실행시키려면 해당 OS에서 실행가능한 JVM만 있으면 된다. 즉, 특정 OS용 JVM만 있다면 Java 애플리케이션은 OS와 하드웨어에 독립적으로 존재할 수 있는것이다.

<br/>

## JVM 실행

<p align="center">
    <img src="../image/JVM_running.png"  width="800" height="auto">
</p>

<br/>

java.exe 명령어가 실행되면서 JVM은 [바이트 코드](../etc/binary_code_&_bytecode.md) 파일(Hello.class)을 메모리로 로드 → **해당 운영체제에 맞게 기계어로 번역** → `main()` 메서드를 찾아 실행

<br/>

## JVM 구성 요소

<p align="center">
    <img src="../image/JVM_component.png"  width="800" height="auto">
</p>

JVM은 크게 클래스 로더(Class Loader), 런타임 데이터 영역(Runtime Data Area), 실행 엔진(Execution Engine)으로 구성되어 있다.

클래스 로드는 컴파일된 자바 바이트코드를 런타임 데이터 영역에 로드하고, 실행 엔진은 바이트코드를 실행한다.

### 클래스 로더(Class Loader)

java.exe 명령어가 실행되어 JVM이 구동되면 클래스 로더는 시작 클래스의 바이트코드 파일을 JVM이 운영체제로부터 할당받은 메모리 영역인 런타임 데이터 영역으로 적재(Loading)하는 일을 한다.

이 때 `main()`를 찾아 `main()`부터 메모리에 적재되는데, `main()`이 실행되면서 필요한 객체들을 동적으로 로드한다. 컴파일 타임이 아니라 자바 애플리케이션이 실행되는 시점에 클래스가 처음으로 참조될 때 해당 클래스를 로드한다(동적 클래스 로딩, Dynamic class loading).

클래스 로더는 메모리 영역에 코드를 적제하는 역할과 클래스 파일의 에러를 검증하고 레퍼런스들을 연결하는 Linking 작업, 초기화(Initialization)까지 수행한다.

### 런타임 데이터 영역(Runtime Data Area)

JVM이 프로그램을 실행하기 위해 운영체제로부터 할당받은 메모리 영역이다.

JVM이 실행되면 JVM 단위로 생성되는 Heap과 Method Area이 생성된다.

**Heap**

- 메서드 안에서 사용되는 객체들을 위한 영역으로 `new`를 통해 생성된 객체, 배열, immutable 객체 등의 값이 저장된다
- 모든 쓰레드가 공유하는 영역이다
- 힙에 저장된 객체에게 할당된 메모리는 가비지 컬렉터에 의해 회수된다

**Method Area**

- 클래스에서 필요한 패키지 클래스 (예를 들어, Math.abs(-10)이 사용된다면 Math 클래스에 바로 접근 가능)
- `static` 변수, `final` 변수
- 필드와 메서드 데이터, 생성자와 메서드의 코드 내용(바이트코드의 내용과 거의 일치하는 내용이 저장된다)
- 런타임 상수 풀
  - 메서드 영역은 JVM이 시작될 때 생성되고, JVM 하나에 하나의 메서드 영역이 생성되지만, 그 중 런타임 상수 풀은 클래스 마다 생성된다
  - 컴파일 타임에 알 수 있는 숫자 리터럴 값부터 런타임에 해석되는 메서드와 필드의 참조까지 포괄하는 여러 종류의 상수가 포함된다


<br/>

JVM이 실행되면 → 힙, 메서드 영역이 생성되고 → JVM의 클래스 로더는 시작 클래스로 지정된 .class 파일을 메서드 영역으로 로딩한다(이 과정을 시작 클래스를 생성한다라고 할 수 있다) → 시작 클래스가 생성되면 런타임 상수 풀도 함께 생성된다  


<br/>

--- 

<br/>

출처 및 참고

- Effective Java - 조슈아 블로크 
- Java의 정석 (남궁 성)
- [우아한테크코스 테코톡 - 던의 JVM의 Garbage Collector](https://www.youtube.com/watch?v=vZRmCbl871I&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=64)
- [#자바가상머신, JVM(Java Virtual Machine)이란 무엇인가?](https://asfirstalways.tistory.com/158)
- [[Java] JDK, JVM 용어 정리 및 프로그램 실행 단계](https://you9010.tistory.com/150)
- [JVM, JRE, JDK의 차이](https://wikidocs.net/257)
- [JVM(Java Virtual Machine), 바이트코드(Byte Code)](https://beststar-1.tistory.com/2)
- [Back to the Essence - Java 컴파일에서 실행까지 - (2)](https://homoefficio.github.io/2019/01/31/Back-to-the-Essence-Java-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EC%97%90%EC%84%9C-%EC%8B%A4%ED%96%89%EA%B9%8C%EC%A7%80-2/)
- [JVM( Java Virtual Machine )이란](https://honbabzone.com/java/java-jvm/)