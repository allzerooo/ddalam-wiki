- [Design Pattern](#design-pattern)
  - [디자인 패턴의 필요성](#디자인-패턴의-필요성)
  - [Singleton Pattern](#singleton-pattern)
    - [두 가지 목적이 있다](#두-가지-목적이-있다)
    - [필요한 경우](#필요한-경우)
    - [가장 간단한 구현](#가장-간단한-구현)
    - [멀티 쓰레드 환경에 안전하게 구현](#멀티-쓰레드-환경에-안전하게-구현)

# Design Pattern

자주 사용하는 설계 패턴을 정형화해서 이를 유형별로 가장 최적의 방법으로 개발할 수 있도록 정해둔 설계

## 디자인 패턴의 필요성

**Gof 디자인 패턴**
```text
소프트웨어 설계를 할 때는 기존의 경험이 매우 중요하다. 그러나 모든 사람들이 다양한 경험을 가지고 있을 수는 없다. 이러한 지식을 공유하기 위해서 나온 것이 GOF의 디자인 패턴이다. 객체지향 개념에 따른 설계 중 재사용할 경우 유용한 설계를 디자인 패턴으로 정리해둔 것이다.
이를 잘 이해하고 활용한다면, 경험이 부족하더라도 좋은 소프트웨어 설계가 가능하다.
```

## Singleton Pattern

### 두 가지 목적이 있다
- 클래스의 인스턴스를 오직 하나만 제공하는 것
- 하나의 인스턴스에 글로벌하게 접근할 수 있는 방법을 제공하는 것

### 필요한 경우
예를 들면 게임의 설정 화면

### 가장 간단한 구현
1. `new`를 사용해 여러번 생성하지 못하도록 하기 → `private`으로 생성자를 만들기
    ```java
    public class Settings {
        private Settings() {}
    }
    ```
2. 클래스 내부에서 글로벌하게 인스턴스에 접근할 수 있도록 만들어주기 → `static`을 제공해주기
   ```java
    public class Settings {

        private static Settings instnace;

        private Settings() {}

        public static Settings getInstance() {
            if (instance == null) {
                instance = new Settings();
            }

            return instance;
        }
    }
    ```

→ 멀티 쓰레드 환경에서 이 구현이 안전할까? 안전하지 않다

왜냐하면, 인스턴스가 생성되기 전에 여러 쓰레드가 `if (instance == null)` 이 조건을 통과할 수 있기 때문이다.

### 멀티 쓰레드 환경에 안전하게 구현
1. `sychronized` 키워드 사용하기
    ```java
    public class Settings {

        private static Settings instnace;

        private Settings() {}

        public static synchronized Settings getInstance() {
            if (instance == null) {
                instance = new Settings();
            }

            return instance;
        }
    }
    ```
    `getInstance()`에 한번에 하나의 쓰레드만 들어올 수 있도록 한다. 하지만 이 방법은 `getInstance()`가 호출될 때마다 동기화를 처리해야 되는 작업 때문에 성능을 저하할 수 있다는 단점이 있다.
2. 이른 초기화(eager initialization) 사용하기
    ```java
    public class Settings {

        private static final Settings INSTANCE = new Settings();

        private Settings() {}

        public static synchronized Settings getInstance() {
            return INSTANCE;
        }
    }
    ```
    클래스가 로딩되는 시점에 static 필드들이 초기화될 때 인스턴스를 생성한다. 하지만 사용되기 전에 인스턴스를 미리 만든다는 점 자체가 단점이 될 수 있다(에를 들어 인스턴스를 만드는 과정이 오래걸리고, 메모리를 많이 사용한다면).
3. double checked locking 사용하기  
    ```java
    public class Settings {

        private static volatile Settings instnace;

        private Settings() {}

        public static Settings getInstance() {
            if (instance == null) {
                synchronized (Settings.class) { // Settings 클래스를 Lock으로 사용
                    if (instance == null) { // 안에서 한번 더 체크
                        instance = new Settings();
                    }
                }
            }

            return instance;
        }
    }
    ```


<br/>

---

<br/>

출처
- 패스트캠퍼스 - 한번에 끝내는 Java/String 웹 개발 마스터(예상국)
- Effective Java(조슈아 블로크)
- [인프런 - 코딩으로 학습하는 GoF의 디자인 패턴(백기선)](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)