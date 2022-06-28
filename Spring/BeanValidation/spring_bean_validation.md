- [Spring이 Bean Validation을 사용하는 방법](#spring이-bean-validation을-사용하는-방법)
  - [검증 순서](#검증-순서)

# Spring이 Bean Validation을 사용하는 방법

Spring Boot는 `spring-boot-starter-validatoin` 라이브러리를 넣으면 자동으로 Bean Validator를 인지하고 스프링에 통합한다

- `LocalValidatorFactoryBean` 을 글로벌 Validator로 등록
    - 애노테이션을 보고 Bean 검증을 수행
    - 검증하려는 Bean에 `@Valid`, `@Validated` 만 적용하면 된다
    - 검증 오류가 발생하면 `FieldError`, `ObjectError` 를 생성해서 `BindingResult` 에 담아준다

- 직접 글로벌 Validator를 직접 등록하면 Spring Boot는 글로벌 Validator를 등록하지 않는다

## 검증 순서

1. `@ModelAttribute` 각각의 필드에 타입 변환 시도 (Request Parameter를 객체 필드에 넣는 작업)
    1. 성공하면 다음으로
    2. 실패하면 `typeMismatch`로 `FieldError` 추가
2. Validator 적용

바인딩에 성공한 필드만 Bean Validation을 적용

예를 들어,

- `itemName` 에 문자 "A" 입력 → 타입 변환 성공 → `itemName` 필드에 BeanValidation 적용
- `price` 에 문자 "A" 입력 → "A"를 숫자 타입 변환 시도 실패 → typeMismatch FieldError 추가 →
  `price` 필드는 Bean Validation 적용 X

<br/>

---

<br/>

참고 및 출처
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)