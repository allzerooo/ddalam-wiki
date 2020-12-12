# 의존관계 자동 주입

## 의존관계 주입 방법

1. 생성자 주입
2. 수정자 주입(setter 주입)
3. 필드 주입
4. 일반 메서드 주입

<br/>

### 생성자 주입

- 생성자 호출 시점에 1번만 호출되는 것이 보장됨
- 변경하는 메서드를 정의하지 않는 한 불변, 필수 의존관계에 사용 가능
- 스프링 빈에 등록된 클래스의 생성자가 1개인 경우 `@Autowired`를 생략해도 자동 주입
- 최근 DI 프레임워크 대부분이 생성자 주입을 권장
  - 애플리케이션 종류 전까지 의존관계를 변경하지 않는 것이 좋다
  - 누군가 실수로 변경할 수 있기 때문에 변경 메서드를 열어두는 것은 좋은 설계 방법이 아니다
  - 의존관계 주입을 위한 메서드를 사용할 때 객체 생성 시 어떤 의존관계를 주입해야 되는지 확인하기 어렵다
  - 프레임워크 없이 순수한 자바 코드를 단위테스트 할 수 있다
  - 필드에 `final` 키워드를 사용할 수 있기 떄문에 의존관계 주입이 누락된 경우 컴파일 시점에 오류를 확인할 수 있다
- 생성자가 1개인 경우 Lombok 라이브러리의 `@RequiredArgsConstructor`를 사용하면 `final`이 있는 필드들의 생성자를 자동으로 만들어준다
  - Lombok이 Java의 애노테이션 프로세서 기능을 이용해 컴파일 시점에 생성자 코드를 자동으로 생성
  ```java
  @Component
  @RequiredArgsConstructor
  public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      private final DiscountPolicy discountPolicy;
  }
  ```

<br/>

### 수정자 주입(setter 주입)

- 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용해 의존관계를 주입하는 방법
- 선택, 변경 가능성이 있는 의존관계에 사용

```java
@Autowired
public void setMemberRepository(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
}
```

<br/>

### 필드 주입

- 필드에 `@Autowired`를 사용해 바로 주입하는 방법
- DI 프레임워크가 없으면 주입할 수 없음 → 외부에서 변경이 불가능해 테스트하기 어려움 (`@SpringBootTest`처럼 스프링 컨테이너를 테스트에 통합한 경우에만 가능)
- `@Configuration` 같은 곳에서만 특별한 용도로 사용

<br/>

### 일반 메서드 주입

- 한번에 여러 필드를 주입할 수 있지만, 일반적으로 잘 사용하지 않음

```java
@Autowired
public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

<br/>

## 주입할 스프링 빈이 없어도 동작하도록, 자동 주입 대상을 옵션으로 처리하는 방법

- `@Autowired(requried = false) : 주입할 대상이 없으면 메서드 자체가 호출이 안됨
  ```java
  @Autowired(required = false)
  public void setNoBean(Member member) { // 메서드가 호출 안됨
      System.out.println(member);         // 출력 안됨
  }
  ```
- `org.springframework.lang.@Nullable` : 주입할 대상이 없으면 `null`이 입력
  ```java
  @Autowired
  public void setNoBean(@Nullable Member member) {
      System.out.println(member);     // null 출력
  }
  ```
- `Optional<>` : 주입할 대상이 없으면 `Optional.empty`가 입력
  ```java
  @Autowired(required = false)
  public void setNoBean(Optional<Member> member) {
      System.out.println(member);     // Optional.empty 출력
  }
  ```
- `@Nullable`, `Optional`은 생성자 자동 주입에서 특정 필드에만 사용해도 된다

<br/>

## 조회된 빈이 2개 이상일 때

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {
```

```java
@Autowired
private DiscountPolicy discountPolicy
```

`@Autowired`는 타입으로 조회하기 때문에 `DiscountPolicy`의 하위 타입인 `FixDiscountPolicy`, `RateDiscountPolicy` 둘다 스프링 빈으로 등록되면 의존관계 자동 주입 시 `NoUniqueBeanDefinitionException` 오류가 발생한다

<br/>

하위 타입으로 지정하는 방법이 있지만

- DIP 위배, 유연성이 떨어진다
- 이름만 다르고 완전히 똑같은 타입의 스프링 빈이 2개 있을 때 해결이 안된다
- 스프링 빈을 수동 등록해서 문제를 해결할 수 있다

<br/>

의존관계 자동 주입으로 해결하는 방법

- `@Autowired` 필드 명 매칭
- `@Qualifier` 사용
- `@Primary` 사용

<br/>

### `@Autowired` 필드 명 매칭

타입 매칭을 시도하고, 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 매칭

```java
@Autowired
private DiscontPolicy rateDiscountPolicy
```

`RateDiscountPolicy`가 정상 주입된다

<br/>

### `@Qualifier`

- 빈 등록시 추가 구분자를 붙여주는 방법. 빈 이름을 변경하는 것은 아니다
- `@Qualifier`로 등록한 이름의 스프링 빈을 추가로 찾는다
- `NoSuchBeanDefinitionException` 예외 발생

```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
```

```java
@Autowired
public OrderServiceImpl(@Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
}
```

<br/>

### `@Primary`

여러 빈이 매칭되면 우선순위를 정하는 방법

```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}
```

### `@Qualifier` 대신 애노테이션 만들어 사용하기

`@Qualifier("mainDiscountPolicy")` 이렇게 문자를 적으면 컴파일시 타입 체크가 안된다

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE}) @Retention(RetentionPolicy.RUNTIME)
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {}
```

```java
@Component
@MainDiscountPolicy
public class RateDiscountPolicy implements DiscountPolicy {}
```

---

<br/>

[출처]

- 인프런 스프링 핵심 원리 기본편 (김영한)
