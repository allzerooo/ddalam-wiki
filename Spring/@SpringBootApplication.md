# `@SpringBootApplication`

Spring Boot 애플리케이션의 시작점

## `@SpringBootApplication` 코드
```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```
- `@SpringBootConfiguration` : 스프링 부트용 `@Configuration`
- `@EnableAutoConfiguration` : 사전에 정의한 라이브러리 빈을 등록
- `@ComponentScan` : 각종 스프링 빈 애노테이션을 베이스 패키지에서부터 스캔하여 스프링 빈으로 스프링 Ioc 컨테이너에 등록

## 속성들
- `exclude`
- `excludeName`
  - `exclude`로 클래스 이름을 지정할 수 없는 경우(ex. import문으로 불러올 수 없는 외부 라이브러리)에 사용할 수 있다
- `scanBasePackages`
- `scanBasePackageClasses`
- `nameGenerator`
- `proxyBeanMethods`

<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)