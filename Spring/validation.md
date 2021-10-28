- [Validation](#validation)
  - [BindingResult](#bindingresult)

# Validation

컨트롤러의 중요한 역할 중 하나는 HTTP 요청이 정상인지 검증하는 것이다. API 방식을 사용하면 API 스펙을 잘 정의해서 검증 오류를 API 응답 결과에 잘 남겨주어야 한다.

## BindingResult
- `@RequestBody`, `@RequestPart` , `@ModelAttribute` 인수 직후에 선언되어야 한다

---

<br/>

출처 및 참고
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)