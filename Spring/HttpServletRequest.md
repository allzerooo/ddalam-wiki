# HttpServletRequest

### 역할

HTTP 요청 메시지를 편리하게 조회할 수 있다.

서블릿은 개발자가 HTTP 요청 메시지를 편리하게 사용할 수 있도록 HTTP 요청 메시지를 파싱한 결과를 `HttpServletRequest` 객체에 담아서 제공한다.

### 기능
- Start line
  - HTTP 메서드
  - URL
  - 쿼리 스트링
  - 스키마, 프로토콜
- 헤더
  - 헤더 조회
- 바디
  - form 파라미터 형식 조회
  - message body 데이터 직접 조회
- 임시 저장소 기능 : 해당 HTTP 요청 시작부터 끝날 때까지 유지되는 임시 저장소 기능
  - 저장 : `request.setAttribute(name, value)`
  - 조회 : `request.getAttribute(name)`
- 세션 관리 기능
  - `request.getSession(create: true)`


<br/>

--- 

<br/>

출처 및 참고

- [스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1/dashboard)