- [Spring Boot Properties](#spring-boot-properties)
  - [Externalized Configuration](#externalized-configuration)
  - [외부 설정의 우선순위](#외부-설정의-우선순위)
  - [설정 파일을 Java 코드로 읽는 방법](#설정-파일을-java-코드로-읽는-방법)

# Spring Boot Properties
- 스프링 부트의 기본 기능 전체를 튜닝하는 부트 전용 설정 프로퍼티
- classpath: application.properties, application.yml로 제어 가능
- 스프링 부트의 기능 거의 대부분을 제어 가능
- 기본값이 세팅되어 있어서 아무것도 쓰지 않아도 작동함
- 스프링 부트를 쓰는데 설정이 자꾸만 별도의 자바 코드로 등장한다? → properties를 찾아보자

## Externalized Configuration
설정 값을 외부로 분리해낸 것

- 서로 다른 환경에서도 사용할 수 있음
- 애플리케이션을 새로 컴파일하지 않고 설정값을 바꿀 수 있음
- 종류
  - Java properties file
  - YAML
  - environment variable
  - command-line argument
  
## 외부 설정의 우선순위
외부 설정을 읽어들이는 순서(아래 설정이 위에서 읽은 것을 덮어씀)

1. 디폴트 프로퍼티
2. @Configuration 클래스에 @PropertySource 로 정의된 것 
3. 설정 파일: application.properties
    - 설정 파일이 여러개 있을 때 우선순위(JAR 패키지 안에 있는지, 밖에 있는지, 그리고 application 뒤에 -이 있는지, 없는지에 따라)
      1. JAR 패키지 안의 application.properties, application.yaml
      2. JAR 패키지 안의, 프로파일이 지정된 파일: application-{profile}.properties 
      3. JAR 패키지 밖의 파일
      4. JAR 패키지 밖의, 프로파일이 지정된 파일

    - 설정 파일의 위치 우선순위
      1. classpath 
         1. classpath:/ 
         2. classpath:/config
      2. 현재 디렉토리 
         1. ../ 
         2. ../config 
         3. ../config/child
4. 4.RandomValuePropertySource
5. OS 환경변수
6. 자바 시스템 프로퍼티: System.getProperties()
7. JNDI 속성: java:comp/env
8. ServletContext - 초기 파라미터
9.  ServletConfig - 초기 파라미터 
10. SPRING_APPLICATION_JSON 안의 프로퍼티들 
11. Command-line arguments
12. 테스트에 사용된 프로퍼티들
13. @TestPropertySource
14. Devtools 글로벌 세팅: $HOME/.config/spring-boot

## 설정 파일을 Java 코드로 읽는 방법
- `@Value`
  - SpEL 로 프로퍼티명을 표현
  - type-safe 하지 않음
  - 필드 주입 방식을 사용할 경우,
    - 인스턴스화 이후에 주입하므로, final 쓸 수 없음
    - 생성자 안에서 보이지 않음 (대안: `@PostConstruct`)
  - 생성자 주입 방식이 가능
  - 프로퍼티 Relaxed binding 지원 (kebab-case only)
  - meta-data 없음 (javadoc은 적용 가능)
- `Environment`
  - 애플리케이션 컨텍스트에서 꺼내오는 방법
  - Environment 빈을 가져오는 방법
  - Spring Boot가 뜨면 Environment, ApplicationContext는 이미 등록이 된 상태이기 때문에 바로 어느 코드에서든 사용할 수 있다는 장점이 있다
  - 눈에잘안들어옴
  ```java
  @Autowired // 생략 가능 (as of Spring Framework 4.3)
  public Test(Environment environment, ApplicationContext app) {
    System.out.println(environment.getPropterty("iam.duration"));
    System.out.println(app.getEnvironment().getProperty("iam.duration"));
  }
  ```
- `@ConfigurationProperties`
  - 자바 클래스로 매핑하므로 type-safe
  - 각 프로퍼티에 대응하는 meta-data 작성 가능
  - Relaxed binding 지원
  - 작성방법
    - 기본
      ```java
      @ConfigurationProperties("iam")
      @Configuration
      public class CustomProperties {
          private Duration duration;

          public Duration getDuration() {
              return duration();
          }

          public void setDuration(Duration duration) {
              this.duration = duration;
          }
      }
      ```
    - `@Configuration` 생략
      ```java
      @ConfigurationProperties("iam")
      public class CustomProperties {
          private Duration duration;

          public Duration getDuration() {
              return duration();
          }

          public void setDuration(Duration duration) {
              this.duration = duration;
          }
      }
      ```
      ```java
      @ConfigurationPropertiesScan
      @SringBootApplication
      public class SpringBootPracticeApplication {...}
      ```
    - `@Bean` 메소드
      ```java
      @Configuration
      public class Config {
          @Bean
          @ConfigurationProperties("iam")
          public CustomProperties customProperties() {
              return new CustomProperties();
          }
      }
      ```
    - `@ConstructorBinding`
      - Immutable한 프로퍼티를 구현할 수 있는 방법(추천)
      ```java
      @ConstructorBinding
      @ConfigurationProperties("iam")
      public class CustomProperties {
          private Duration duration;

          public CustomProperties(@DefaultValue("1") Duration duration) {
              this.duration = duration;
          }

          public Duration getDuration() {
              return duration;
          }
      }
      ```


<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)