# H2 DB

메모리, 파일에 쓸 수 있는 DB
- 메모리 모드로 사용하면 애플리케이션을 중단했을 때 데이터도 삭제된다

<br/>

```gradle
runtimeOnly 'com.h2database:h2'
```
- h2 DB와 서비스를 같은 포트에 띄워준다

<br/>

```yml
spring:
    h2:
        console:
            enabled: true
```
- `console.enabled: true`를 설정하면 Spring Boot가 시작될 때 `jdbc:h2:mem:XXXXXX`와 같이 JDBC URL 정보가 뜨고, `/h2-console` 경로로 데이터베이스에 접근할 수 있다