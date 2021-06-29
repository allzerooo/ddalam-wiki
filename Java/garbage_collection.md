# 가비지 컬렉션 (Garbage Collection)

메모리 관리 방법 중 하나로, 시스템에서 더 이상 사용되지 않는 동적 할당된 메모리를 찾아 다시 사용 가능한 자원으로 자동으로 회수하는 것을 말한다. 시스템에서 가비지 컬렉션을 수행하는 부분을 가비지 컬렉터(Garbage Collector)라고 부른다.

Java에서 가비지 컬렉션의 대상이 되는 것은 JVM의 힙 메모리에 할당된 객체로, 호출 스택에서 도달 불가능한 객체들이 대상이 된다.

<br/>

## 가비지 컬렉터의 역할
1. 메모리 할당
2. 사용중인 메모리 인식
3. 사용하지 않는 메모리 인식

<br/>

## JVM의 Heap 메모리

Heap은 Young, Old, Perm 세 영역으로 나뉜다.

### Young 영역
새롭게 생성한 객체가 위치한다

### Old 영역
Young 영역에서 참조할 수 있는 상태를 유지해 살아남은 객체가 위치한다

### Perm 영역
클래스와 메서드 정보와 같이 자바 언어 레벨에선느 거의 사용되지 않는 영역

<br/>

이 영역은 Eden 영역과 Servivor Space 영역으로 나뉜다.

일단 메모리에 객체가 생성되면 Eden 영역에 객체가 지정되고, Eden 영역에 데이터가 어느 정도 쌓이면 Servivor Space로 옮겨진다.

그러다가 더 큰 객체가 생성되거나 더 이상 Young 영역에 공간이 남지 않으면 객체들은 Old 영역으로 이동하게 된다.

<br/>
Young 영역에서 발생한 GC를 Minor GC, 나머지 두 영역에서 발생한 GC를 Major GC(Full GC)라고 한다.





<br/>

---

<br/>

참고
- [가비지 컬렉션, 컬렉터(Garbage Collection)란?](https://blog.metafor.kr/163)
- Java의 정석(남궁 성)
- [[성능튜닝] 가비지 컬렉터(GC) 이해하기](https://12bme.tistory.com/57)