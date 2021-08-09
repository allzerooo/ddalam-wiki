# JVM (Java virtual machine)

Java를 실행하기 위한 가상 머신[( Virtual Machine )](../etc/virtual_machine.md). 즉, 컴퓨터에서 Java를 실행하기 위해 소프트웨어로 구현된 가상의 하드웨어로 컴퓨터와 거의 동일한 기능을 수행할 수 있는 컴퓨터의 가상화된 인스턴스이다.

Java로 작성된 애플리케이션은 모두 이 가상 컴퓨터(JVM)에서만 실행되기 때문에 Java 애플리케이션이 실행되기 위해서는 반드시 JVM이 필요하다.

<br/>

<p align="center">
    <img src="../image/jvm.png"  width="220" height="auto">
</p>

<br/>

Java 애플리케이션은 JVM을 거쳐 OS → 하드웨어로 전달된다. 만약, Java 애플리케이션과 OS 사이에 JVM이 없다면 다른 OS에서 Java 애플리케이션을 실행시키기 위해서는 애플리케이션을 그 OS에 맞게 변경해야한다. 하지만 Java 애플리케이션은 JVM 하고만 상호작용을 하면 된다. 다른 OS에서 Java 애플리케이션을 실행시키려면 해당 OS에서 실행가능한 JVM만 있으면 된다. 즉, 특정 OS용 JVM만 있다면 Java 애플리케이션은 OS와 하드웨어에 독립적으로 존재할 수 있는것이다.

<br/>

## Java 파일 컴파일 방법 + 실행하는 방법 + JVM 구동

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

java.exe 명령어가 실행되어 JVM이 구동되고, 바이트코드 파일이 JVM에 의해 읽어들여지면 클래스로더는 이 바이트코드를 JVM이 운영체제로부터 적재받은 메모리 영역인 런타임 데이터 영역으로 적재하는 일을 한다.

이 때 `main()`를 찾아 `main()`부터 메모리에 적재되는데, `main()`이 실행되면서 필요한 객체들을 동적으로 로드한다. 컴파일 타임이 아니라 자바 애플리케이션이 실행되는 시점에 클래스가 처음으로 참조될 때 해당 클래스를 로드한다(동적 클래스 로딩, Dynamic class loading).

클래스 로더는 메모리 영역에 코드를 적제하는 역할과 클래스 파일의 에러를 검증하고 레퍼런스들을 연결하는 Linking 작업, 초기화(Initialization)까지 수행한다.


<br/>

## JVM의 메모리 구조

java.exe로 JVM이 시작되면 JVM은 운영체제로부터 프로그램을 수행하는데 필요한 메모리영역(Runtime Data Area)을 할당받고, 이 메모리를 여러 영역으로 나누어 관리한다

<p align="center">
    <img src="../image/JVM_memory.png"  width="300" height="auto">
</p>

### Method(Static) Area
- 모든 스레드가 공유하는 영역
- 클래스에 대한 정보

### Stack Area
- 스레드 마다 자신의 스택 영역을 가진다
- 메서드가 호출되면 메서드를 위한 메모리가 할당되며, 이 메모리에는 메서드의 지역변수, 중간 결과 등을 저장하는데 사용되고, 메서드가 작업을 마치면 반환된다.
- 인스턴스에 대한 참조값이 저장되며, 인스턴스는 Heap Area에 생성된다.

### Heap Area
- 모든 스레드가 공유하는 영역


<br/>

## JVM 역할
- JVM이 구동되고 있는 하드웨어에 맞는 기계어(Binary code)로 변환한다


<br/>

--- 

<br/>

출처

- Effective Java - 조슈아 블로크 
- Java의 정석 (남궁 성)
- [우아한테크코스 테코톡 - 던의 JVM의 Garbage Collector](https://www.youtube.com/watch?v=vZRmCbl871I&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=64)
- [#자바가상머신, JVM(Java Virtual Machine)이란 무엇인가?](https://asfirstalways.tistory.com/158)
- [[Java] JDK, JVM 용어 정리 및 프로그램 실행 단계](https://you9010.tistory.com/150)
- [JVM, JRE, JDK의 차이](https://wikidocs.net/257)
- [JVM(Java Virtual Machine), 바이트코드(Byte Code)](https://beststar-1.tistory.com/2)