- [Spring Boot 라이브러리](#spring-boot-라이브러리)
  - [`spring-boot-starter`](#spring-boot-starter)
    - [`spring-boot-starter-logging`](#spring-boot-starter-logging)
    - [`spring-core`](#spring-core)
  - [`spring-boot-starter-web`](#spring-boot-starter-web)
    - [`spring-boot-starter-tomcat`](#spring-boot-starter-tomcat)
    - [`spring-webmvc`](#spring-webmvc)
  - [`spring-boot-starter-data-jpa`](#spring-boot-starter-data-jpa)
    - [`spring-boot-starter-jdbc`](#spring-boot-starter-jdbc)
    - [`hibernate-core`](#hibernate-core)
    - [`spring-data-jpa`](#spring-data-jpa)

# Spring Boot 라이브러리

`spring-boot-starter-XXX`는 모두 `spring-boot-start`에 의존하고 있다

## `spring-boot-starter`
### `spring-boot-starter-logging`
- logback이 기본
- slf4j는 로그를 찍기 위한 인터페이스, 이 구현체로 log4j를 쓰거나, logback을 쓰거나
### `spring-core`

## `spring-boot-starter-web`

### `spring-boot-starter-tomcat`
- 톰캣 (웹서버)
- Spring Boot 프로젝트를 실행했을 때 8080 포트에 서버가 뜨는게 가능했던 이유

### `spring-webmvc`
- Spring Web MVC
  
## `spring-boot-starter-data-jpa`

### `spring-boot-starter-jdbc`
- 데이터베이스 커넥션 등을 다 가져다 써야되기 때문에 
- HicariCP : 커넥션 풀, Spring Boot 2부터 기본
### `hibernate-core`
### `spring-data-jpa`