# HTTP 통신

### ```HttpEntity```

- header와 body로 구성된 HTTP request 또는 response를 나타낸다
- 일반적으로 ```RestTemplate```와 함께 사용된다
  ```java
  HttpHeaders headers = new HttpHeaders();
  headers.setContentType(MediaType.APPLICATION_JSON);
  String requestBodyJson = JSON_STRING;
  HttpEntity<String> entity = new HttpEntity<String>(requestBodyJson, headers);
  RestTemplate restTemplate = new RestTemplate();
  ResponseEntity<JsonNode> response = restTemplate.postForEntity(url, entity, JsonNode.class);
  ```
- ```@Controller``` 메서드의 반환 값으로 SpringMVC에서도 사용할 수 있다
- ```HttpEntity```를 상속받는 클래스로 ```RequestEntity```와 ```ResponseEntity```가 있다
  - ```RequestEntity```는 ```RestTemplate```을 사용해 요청할 때, ```@Controller``` 메서드에서 request의 입력을 위해 사용된다
  - ```ResponseEntity```는 ```HttpStatus``` 클래스를 사용해 상태 코드를 표현할 수 있으며 ```RestTemplate```을 사용해 응답을 받을 때, ```@Controller``` 메서드에서 반환 값으로 사용된다

<br/>

---
출처
- [spring.io - HttpEntity](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/HttpEntity.html)
- [spring.io - RequestEntity](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/RequestEntity.html)
- [spring.io - ResponseEntity](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/ResponseEntity.html)