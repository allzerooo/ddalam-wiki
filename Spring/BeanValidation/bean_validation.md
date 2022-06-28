- [Bean Validation](#bean-validation)
  - [의존관계 추가](#의존관계-추가)
  - [javax.validation, org.hibernate.validator](#javaxvalidation-orghibernatevalidator)
  - [Validator 생성](#validator-생성)
  - [검증 실행](#검증-실행)

# Bean Validation

클래스 필드에 대한 반복되는 검증 로직을 모든 프로젝트에 적용할 수 있게 공통화하고, 표준화한 것

- 특정 구현체가 아니라 Java에서 정한 기술 표준
- 검증 애노테이션과 여러 인터페이스의 모음
    - 일반적으로 사용하는 구현체는 하이버네이트 Validator

애노테이션 하나로 검증 로직을 편리하게 적용할 수 있다

## 의존관계 추가

```
implementation 'org.springframework.boot:spring-boot-starter-validation'
```

- jakarta.valication-api : Bean Validation 인터페이스
- hibernate-validator : 구현체

## javax.validation, org.hibernate.validator

- javax.validation : 표준적으로 제공되는 것으로 어떤 구현체에서도 동작
- org.hibernate.validator : 하이버네이트 validator 구현체를 사용할 때만 제공되는 검증 기능

## Validator 생성

```java
ValidatorFactory factory = Validation.buildDefatulValidatorFactory();
Validator validator = factory.getValidator();
```

- 스프링에는 이미 Validator가 통합되어 있기 때문에 직접 코드를 작성하지 않아도 된다

## 검증 실행

```
Set<ConstraintViolation<Item>> violations = validator.validate(item);
for (<ConstraintViolation<Item>> violation: vioations) {
	System.out.println("violation = " + violation);
	System.out.println("violation = " + violation.getMessage());
}
```

- `Set`에는 `ConstraintViolation`이라는 검증 오류가 담긴다. 결과가 비어있으면 검증 오류가 없는 것
    - `ConstraintViolation` 출력 결과를 보면, 검증 오류가 발생한 객체, 필드, 메시지 정보등 다양한 정보를 확인할 수 있다

<br/>

---

<br/>

참고 및 출처
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)