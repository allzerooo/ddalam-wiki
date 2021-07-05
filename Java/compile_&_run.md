# Java 코드 컴파일 및 실행

<p align="center">
    <img src="../image/JRE_JDK_JVM.jpeg"  width="460" height="auto">
</p>

1. Java 코드를 작성
2. JDK의 Java Compiler로 .java 파일 컴파일하기 (bytecode 생성)
   ```bash
   $ javac Hello.java
   ```
3. 2번 과정으로 생성된 **Java Bytecode**를 JVM에서 하드웨어에 맞는 기계어로 변환
4. Java 애플리케이션 실행
   ```bash
   $ java Hello
   ```

<br/>

## 바이트코드

JVM이 실행하는 명령어 형태

<br/>

---

<br/>

참고
- [Differences between JDK, JRE and JVM](https://www.geeksforgeeks.org/differences-jdk-jre-jvm/)
- [Live Study 1주차 - JVM은 무엇이며 자바 코드는 어떻게 실행하는 것인가](https://youngjinmo.github.io/2021/01/livestudy-week-01/#bytecode)