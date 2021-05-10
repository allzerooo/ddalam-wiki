# JVM (Java virtual machine)

Java를 실행하기 위한 가상 머신. 즉, 컴퓨터에서 Java를 실행하기 위해 소프트웨어로 구현된 가상의 하드웨어로 컴퓨터와 거의 동일한 기능을 수행할 수 있는 컴퓨터의 가상화된 인스턴스이다. [( Virtual Machine )](../etc/virtual_machine.md)

Java로 작성된 애플리케이션은 모두 이 가상 컴퓨터(JVM)에서만 실행되기 때문에 Java 애플리케이션이 실행되기 위해서는 반드시 JVM이 필요하다.

<br/>

<!-- <p align="center">
    <img src="../image/JVM.png"  width="220" height="auto">
</p> -->

<br/>

Java 애플리케이션은 JVM을 거쳐 OS → 하드웨어로 전달된다. 만약, Java 애플리케이션과 OS 사이에 JVM이 없다면 다른 OS에서 Java 애플리케이션을 실행시키기 위해서는 애플리케이션을 그 OS에 맞게 변경해야한다. 하지만 Java 애플리케이션은 JVM 하고만 상호작용을 하면 된다. 다른 OS에서 Java 애플리케이션을 실행시키려면 해당 OS에서 실행가능한 JVM만 있으면 된다. 즉, 특정 OS용 JVM만 있다면 Java 애플리케이션은 OS와 하드웨어에 독립적으로 존재할 수 있는것이다.

<br/>

### JVM 구동

<p align="center">
    <img src="../image/steps_to_run_java.png"  width="600" height="auto">
</p>

<br/>

java.exe 명령어가 실행되면서 JVM은 바이트 코드 파일(Hello.class)을 메모리로 로드 → 해당 운영체제에 맞게 기계어로 번역 → `main()` 메서드를 찾아 실행

<br/>

### JVM의 메모리 구조

java.exe로 JVM이 시작되면 JVM은 운영체제로부터 프로그램을 수행하는데 필요한 메모리영역(Runtime Data Area)을 할당받고, 이 메모리를 여러 영역으로 나누어 관리한다


<br/>
<br/>

- 운영체제의 메모리 영역에 접근하여 메모리를 관리하는 프로그램
- JVM은 배열에 접근할 때마다 경계를 넘지 않는지 검사한다

<br/>

--- 

<br/>

출처

- Effective Java - 조슈아 블로크 
- Java의 정석 (남궁 성)
- [우아한테크코스 테코톡 - 던의 JVM의 Garbage Collector](https://www.youtube.com/watch?v=vZRmCbl871I&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=64)
- [#자바가상머신, JVM(Java Virtual Machine)이란 무엇인가?](https://asfirstalways.tistory.com/158)
- [[Java] JDK, JVM 용어 정리 및 프로그램 실행 단계](https://you9010.tistory.com/150)