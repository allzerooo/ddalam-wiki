# JVM (Java virtual machine)

Java를 실행하기 위한 가상 머신. 즉, 컴퓨터에서 Java를 실행하기 위해 소프트웨어로 구현된 가상의 하드웨어로 컴퓨터와 거의 동일한 기능을 수행할 수 있는 컴퓨터의 가상화된 인스턴스이다. [( Virtual Machine )](../etc/virtual_machine.md)

Java로 작성된 애플리케이션은 모두 이 가상 컴퓨터(JVM)에서만 실행되기 때문에 Java 애플리케이션이 실행되기 위해서는 반드시 JVM이 필요하다.

<br/>

<p align="center">
    <img src="../image/jvm.png"  width="220" height="auto">
</p>

<br/>

- 운영체제의 메모리 영역에 접근하여 메모리를 관리하는 프로그램
- JVM은 배열에 접근할 때마다 경계를 넘지 않는지 검사한다

<br/>

--- 

<br/>

출처

- Effective Java - 조슈아 블로크 
- [우아한테크코스 테코톡 - 던의 JVM의 Garbage Collector](https://www.youtube.com/watch?v=vZRmCbl871I&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=64)
- Java의 정석 (남궁 성)