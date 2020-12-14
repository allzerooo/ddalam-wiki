# 빈 생명주기 콜백

애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종류하는 작업을 진행하려면 객체의 초기화와 종료 작업이 필요하다

<br/>

### 스프링 빈의 이벤트 라이프사이클
```
스프링 컨테이너 생성 → 스프링 빈 생성(객체 생성) → 의존관계 주입 → 초기화 콜백 → 사용 → 소멸전 콜백 → 스프링 종료
```
- 스프링 빈은 객체를 생성하고, 의존관계 주입이 다 끝난 다음에 필요한 데이터를 사용할 수 있는 준비가 완료된다
- 초기화 작업은 의존관계 주입이 완료된 다음 호출해야 한다
- 스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다(스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다)
- `초기화 콜백` : 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출
- `소멸전 콜백` : 빈이 소멸되기 직전에 호출

<br/>

## 스프링이 지원하는 빈 생명주기 콜백
- 인터페이스(InitializingBean, DisposableBean)
- 설정 정보에 초기화 메서드, 종료 메서드 지정
- `@PostConstruct`, `@PreDestory` 애노테이션 지원

<br/>

### 인터페이스(InitializingBean, DisposableBean)
```java
public class NetworkClient implements InitializingBean, DisposableBean {
    public NetworkClient() {
        // 생성자
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        // 초기화 코드
    }

    @Override
    public void destory() throws Exception {
        // 소멸전 코드
    }
}
```
- `InitializingBean`은 `afterPropertiesSet()` 메서드로 초기화를 지원
- `DisposableBean`은 `destroy()` 메서드로 소멸을 지원
- 단점
    - 두 인터페이스는 스프링 전용 인터페이스로, 코드가 스프링에 의존하게 된다
    - 메서드 이름을 변경할 수 없다
    - 외부 라이브러리에 초기화, 소멸 메서드를 적용할 수 없다

<br/>

### 설정 정보에 초기화 메서드, 종료 메서드 지정
```java
public class NetworkClient {
    public NetworkClient() {
        // 생성자
    }

    @Override
    public void init() {
        // 초기화 코드
    }

    @Override
    public void close() {
        // 소멸전 코드
    }
}
```
- 설정 정보에 `@Bean(initMethod = "init", destroyMethod = "close")`와 같이 초기화, 소멸 메서드를 지정
- 메서드 이름을 자유롭게 줄 수 있다
- 스프링 빈이 스프링에 의존하지 않는다
- 코드가 아니라 설정 정보를 사용하기 때문에 외부 라이브러리에도 초기화, 소멸 메서드를 적용할 수 있다
- `@Bean`의 `destroyMethod` 속성의 기본값은 `inferred`로 대부분의 라이브러리가 사용하는 종료 메서드인 `close`, `shutdown` 메서드를 추론해 자동으로 호출해준다. 추론 기능을 사용하기 싫으면 `distroyMethod=""`로 설정하면 된다

---

<br/>

[출처]

- 인프런 스프링 핵심 원리 기본편 (김영한)