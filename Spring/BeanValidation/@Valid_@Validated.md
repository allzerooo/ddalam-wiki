- [`@Valid`, `@Validated`]
  - [`@Valid`]
  - [`@Validated`]

# `@Valid`, `@Validated`

## `@Valid`

- `javax.validation.@Valid` 를 사용하려면 의존관계가 필요하다

    ```java
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    ```

  이 때 추가되는 jakarta.valication-api에 포함되어 있다

- 자바 표준 검증 애노테이션

## `@Validated`

- 스프링 전용 애노테이션
- 내부에 `groups` 라는 기능을 포함

<br/>

---

<br/>

참고 및 출처
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)