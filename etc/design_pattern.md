- [Design Pattern](#design-pattern)
    - [디자인 패턴의 필요성](#디자인-패턴의-필요성)

# Design Pattern

자주 사용하는 설계 패턴을 정형화해서 이를 유형별로 가장 최적의 방법으로 개발할 수 있도록 정해둔 설계

## 디자인 패턴의 필요성

**Gof 디자인 패턴**
```text
소프트웨어 설계를 할 때는 기존의 경험이 매우 중요하다. 그러나 모든 사람들이 다양한 경험을 가지고 있을 수는 없다. 이러한 지식을 공유하기 위해서 나온 것이 GOF의 디자인 패턴이다. 객체지향 개념에 따른 설계 중 재사용할 경우 유용한 설계를 디자인 패턴으로 정리해둔 것이다.
이를 잘 이해하고 활용한다면, 경험이 부족하더라도 좋은 소프트웨어 설계가 가능하다.
```

## Singleton Pattern

### public static member를 사용하는 방법
```java
public class Speaker {
    public static final Speaker INSTANCE = new Speaker();
    private Speaker(){}
}
```


<br/>

---

<br/>

출처
- 패스트캠퍼스 - 한번에 끝내는 Java/String 웹 개발 마스터(예상국)
- Effective Java(조슈아 블로크)