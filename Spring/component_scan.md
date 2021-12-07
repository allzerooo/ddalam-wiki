-[Component Scan](#component-scan)
  - [Spring Bean을 등록하는 방법](#spring-bean을-등록하는-방법)
    - [설정 정보에 등록한 Bean들을 나열](#설정-정보에-등록한-bean들을-나열)
  - [Component Scan 적용 방법](#component-scan-적용-방법)
  - [`@ComponentScan` 속성들](#componentscan-속성들)
  - [`@ComponentScan`을 사용할 때 의존관계 주입](#componentscan을-사용할-때-의존관계-주입)
  - [`@Component` 외 Component Scan 대상](#component-외-component-scan-대상)

# Component Scan

## Spring Bean을 등록하는 방법

### 설정 정보에 등록한 Bean들을 나열
설정 정보가 커지고, Bean을 누락하는 문제, 일일이 등록해야 되는 문제가 발생한다. Spring은 설정 정보가 없어도 자동으로 Spring Bean을 등록하는 **컴포넌트 스캔** 기능을 제공한다

### Component Scan
`@Component`가 붙은 클래스를 스캔해서 Spring Bean으로 등록
- 등록되는 Bean 이름은 앞글자를 소문자로 한 클래스 명
- Bean 이름 직접 등록 가능

## Component Scan 적용 방법

```java
@Configuration
@ComponentScan(
    basePackages = "study.hello",
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = Configuration.class))
public class AppConfig {
}
```

`@ComponentScan`에 지정된 `basePackages` 또는 `basePackageClasses`에서부터 Spring이 모든 `@Component`를 탐색한다. 만약 지정된 base 정보가 없다면 `@ComponentScan`이 붙은 클래스의 패키지가 시작 위치가 된다. 

`@ComponentScan`은 Spring Bean 등록을 위한 설정 정보(`@Configuration`이 있는)에 붙여주거나, Spring Boot 애플리케이션의 시작점인 <a href="https://github.com/ddalam/ddalam-wiki/blob/master/Spring/@SpringBootApplication.md">@SpringBootApplication</a>에 `@ComponentScan`이 포함되어 있기 때문에 `@SpringBootApplication`를 프로젝트 루트에 두는 것이 관례이다

## `@ComponentScan` 속성들
- `basePackages` : 컴포넌트를 스캔할 패키지의 시작 위치를 지정
- `basePackageClasses` : 지정한 클래스의 패키지를 탐색 시작 위치로 지정
- `includeFilters` : 컴포넌트 스캔 대상을 추가로 지정한다
- `excludeFilters` : 컴포넌트 스캔에서 제외할 대상을 지정한다

## `@ComponentScan`을 사용할 때 의존관계 주입
설정 정보를 사용해 Spring Bean을 등록할 때는 의존관계를 직접 명시했는데 컴포넌트 스캔을 사용할 때는 설정 정보를 사용하지 않기 때문에 의존관계 주입을 각 클래스에서 해결해야 한다

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

## `@Component` 외 Component Scan 대상
각 annotation의 소스 코드에 `@Component`가 포함되어 있다
- `@Controlller`
- `@Service`
- `@Repository`
- `@Configuration`


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

---

<br/>

출처 및 참고
- 인프런 스프링 핵심 원리 기본편 (김영한)
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)
