# 메시지, 국제화

## 메시지
다양한 메시지를 한 곳에서 관리하도록 하는 기능

## 국제화
메시지를 나라별로 별도로 관리하는 것

<br/>

스프링 부트는 `MessageSource`를 자동으로 스프링 빈으로 등록해준다. 별도의 설정을 하지 않으면 `messages`라는 이름으로 기본 등록이 되며, `messages_en.properties`(영어로 들어왔을 때), `messages_ko.properties`(한국어로 들어올 때), `messages.properties`(앞 두 파일에서 못찾았을 때) 파일만 등록하면 작동으로 인식된다.

메시지 소스를 변경하고 싶을 때는 `application.properties`에서 `spring.messages.basename` 값을 수정해주면 된다.

```
spring.messages.basename=messages,config.i18n.messages
```


---

<br/>

출처 및 참고
- [스프링 MVC 2편 - 백엔드 웹 개발 활용 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)