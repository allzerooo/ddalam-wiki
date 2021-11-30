# Spring Framework와 Spring Boot

Spring은 Spring Framework, Spring Boot, Spring Data 등 여러 프로젝트들의 모임이다. 또한 Spring의 각 프로젝트는 하위 모듈을 갖는다(Spring Framework는 Spring Web MVC, Spring AOP, Spring Web Flux와 같은 모듈을 갖는다). 그리고 Spring Framework을 제외한 여러 프로젝트들은 Spring Framework을 기반으로 작동한다.

Spring Boot는 Spring Framework 기반의 애플리케이션을 쉽게 개발할 수 있게 해준다. Spring Framework은 초기 설정이 많다. 그리고 대부분 비슷하게 설정해서 사용하는 것들이 있다. Spring Boot는 이런 설정의 복잡도를 낮춰 약간의 설정만으로 Spring 애플리케이션을 만들 수 있게 해줄 뿐이지 Spring Framework와 별개로 사용할 수 있는 것이 아니다.


## Spring Boot로 Spring Framework를 사용할 때 달라지는 점

### 의존성 관리(spring-boot-starter, spring-boot-starter-parent)

Spring Framework은 필요한 모듈의 의존성을 각각 다운받고, 버전도 직접 명시해야 했다. 이 때 모듈간 버전 차이로 충돌이 발생하면 이 또한 개발자가 알아서 처리해야했다. Spring Boot는 spring-boot-starter를 통해 관련 있는 모듈들의 모음을 제공하며, spring-boot-starter-parent를 통해 각 모듈의 현재 Spring Boot 버전에 가장 적합한 버전을 제공해준다.

starter-* 의 부모는 spring-boot-starter-parent이고, spring-boot-starter-parent의 부모는 spring-boot-dependencies이다. 이 하위에 보면 starter-* 의 버전이 명시되어 있다. 그리고 starter-*의 내부에 각 모듈들의 버전이 명시되어 있다.

이런 방법으로 현재 Spring Boot 버전에 적합한 모듈의 버전을 관리하기 때문에 Spring Boot가 관리하는 모듈은 버전을 명시하지 않아도 되는 것이다. 이로 인해 개발자들이 각 모듈과 버전을 직접 찾아 명시하는 수고로움이 줄어들었다.

### 자동 설정(`@EnableAutoConfiguration`)

Spring Framework은 많은 환경 설정이 필요하다. 예를 들어, Spring Framework은 데이터베이스 연결에 필요한 datasource configuration을 직접 xml 또는 Java 코드로 등록하고, 경우에 따라 View에 대한 설정도 해줘야 한다. 하지만 Spring Boot는 의존성으로 추가된 라이브러리와 yml의 데이터베이스 설정 정보를 참고해 datasource configuration을 자동으로 설정해준다.

`@SpringBootApplication` 애노테이션의 내부에는`@EnableAutoConfiguration` 애노테이션이 있다. `@EnableAutoConfiguration` 의 내부에는 `@Import(AutoConfigurationImportSelector.class)` 가 있는데 이 내부의 `selectImports()` 메서드가 자동 설정할 빈을 결정한다. `getAutoConfigurationEntry()` 메서드 내부를 보면 `getCadidateConfigurations()` 가 있는데 이 메서드의 내부에서 META-INF/spring.factories에 정의된 자동 설정 클래스들을 불러온다. META-INF/spring.factories에 정의된 모든 클래스를 빈으로 등록하는 것은 아니며, 각 클래스의 내부에 보면 `@Conditional` 로 시작하는 애노테이션들의 조건에 해당하는 클래스들만 빈으로 등록된다.

spring-configuration-metadata는 자동 설정에 사용할 프로퍼티 정의 파일로, application.yml에 작성한 값으로 프로퍼티를 세팅한 후, 구현되어 있는 자동 설정에 값을 주입시켜준다.

### 내장 WAS(Embedded WAS Tomcat, Jetty, Undertow)

Spring Framework을 사용해 웹 애플리케이션을 배포하려면 애플리케이션을 war로 패키징하고, WAS(Tomcat, Jetty, Undertow)를 별도로 설치해 war 파일을 올려야 한다. Spring Boot는 WAS가 내장되어 있어 jar로 자동 패키징하여 바로 사용할 수 있다. 내장 서버가 작동하는 원리도 Spring Boot가 제공하는 자동 설정에 의해 가능한 것이다(Tomcat이 기본으로 설정되어 있다).

### 모니터링(Actuator)

Spring Boot의 모듈 중 하나인 Actuator는 애플리케이션의 관리 및 모니터링을 지원해준다. Actuator는 상용 서비스 수준에서 필요로 할 모니터링 기능을 엔드포인트로 미리 만들어서 제공해준다. 

<br/>

---

<br/>

참고 및 출처

- [[10분 테코톡] 에어의 Spring vs Spring Boot](https://www.youtube.com/watch?v=Y11h-NUmNXI&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=62)