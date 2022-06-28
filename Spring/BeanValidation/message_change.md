- [오류 메시지 변경](#오류-메시지-변경)

# 오류 메시지 변경

Bean Validation을 적용하고 `BindingResult` 에 등록된 검증 오류 코드를 보면, 오류 코드가 애노테이션 이름으로 등록된걸 확인할 수 있다

```
NotBlank.item.itemName
Range.item.price
```

<br/>

NotBlank 와 같은 오류 코드를 기반으로 `MessageCodesResolver` 를 통해 다양한 메시지 코드가 순서대로 생성된다

<br/>

**@NotBlank**

- NotBlank.item.itemName
- NotBlank.itemName
- NotBlank.java.lang.String
- NotBlank

<br/>

`errors.properties` 에 오류 메시지를 등록하면 변경 가능하다

<br/>

메시지를 찾는 순서는,

1. 생성된 메시지 코드 순서대로 `messageSource` 에서 찾기
2. 애노테이션의 `message` 속성 값 (`@NotBlank(message = “공백!”)`)
3. 라이브러리가 제공하는 기본 값

<br/>

---

<br/>

참고 및 출처
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)