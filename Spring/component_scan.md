# 컴포넌트 스캔

- 스프링 빈을 등록할 때 설정 정보에 등록할 빈들을 나열하는 방법은

  ```plain text
  설정 정보가 커지고
  빈을 누락하는 문제
  일일이 등록해야 되는 문제
  ```

  가 발생한다 → 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 **컴퍼넌트 스캔**이라는 기능을 제공한다

- `@Component`가 붙은 클래스를 스캔해서 스프링 빈으로 등록

  - 등록되는 빈 이름은 앞글자를 소문자로 한 클래스 명
  - 빈 이름 직접 등록도 가능

- `@Component` 외에도 컴포넌트 스캔 대상이 있다 → 각 annotation 소스 코드에 `@Component`가 포함되어 있다
  - `@Controlller`
  - `@Service`
  - `@Repository`
  - `@Configuration`

<br/>

### 컴포넌트 스캔 적용 방법

1. 설정 정보에 `@ComponentScan` 붙여주기

```java
@Configuration
@ComponentScan(
    basePackages = "study.hello",
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = Configuration.class))
public class AppConfig {
}
```

- `basePackages` : 컴포넌트를 스캔할 패키지의 시작 위치를 지정
- `basePackageClasses` : 지정한 클래스의 패키지를 탐색 시작 위로 지정
- 지정하지 않으면 `@ComponentScan`이 붙은 클래스의 패키지가 시작 위치
  - 스프링 부트는 스프링 부트의 대표 시작 정보인 `@SpringBootApplication`에 `@ComponentScan`이 들어있고, `@SpringBootApplication`를 프로젝트 루트에 두는 것이 관례이다
- `includeFilters` : 컴포넌트 스캔 대상을 추가로 지정한다
- `excludeFilters` : 컴포넌트 스캔에서 제외할 대상을 지정한다.

2. 빈으로 등록할 클래스에 `@Component` 붙여주기
3. 의존관계 주입하기
   - 설정 정보를 사용해 스프링 빈을 등록할 때는 의존관계도 직접 명시했는데 컴포넌트 스캔을 사용할 때는 설정 정보를 사용하지 않기 때문에 의존관계 주입도 각 클래스에서 해결해야 한다

```java
@Component
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    ...
}
```

<br/>

### 스프링 빈 중복 등록

- 자동 빈 등록 vs 자동 빈 등록
  - `ConflictingBeanDefinitionException` 예외 발생
- 수동 빈 등록 vs 자동 빈 등록

  - 수동 빈이 자동 빈을 오버라이딩
  - 최근 스프링 부트에서는 수동 빈 등록과 자동 빈 등록이 충돌나면 오류가 발생하도록 기본 값을 바꾸었다

        ```plain text
        Consider renaming one of the beans or enabling overriding by setting
        spring.main.allow-bean-definition-overriding=true
        ```

    💡 수동 빈 등록, 자동 빈 등록 오류시 스프링 부트 에러

    <br/>

[출처]

- 인프런 스프링 핵심 원리 기본편 (김영한)
