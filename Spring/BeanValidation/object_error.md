- [Object 오류 처리](#Object-오류-처리)
  - [`@ScriptAssert()`]

# Object 오류 처리

`ObjectError` 처리 방법

## `@ScriptAssert()`

```java
@Data
@ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000")
public class Item {
	//...
}
```

적용하면 메시지 코드도 생성된다

- ScriptAssert.item
- ScriptAssert

<br/>

---

<br/>

참고 및 출처
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)