- [DataSource](#datasource)
  - [HikariCP](#hikaricp)

# DataSource

- hikari, tomcat, dbcp2, oracleucp와 같은 연결 풀을 사용할 수 있다
- 이 중 Spring Boot는 특정 구현체를 선택하기 위해 다음 알고리즘을 사용한다
  - 성능과 동시성을 위해 HikariCP를 사용할 수 있으면 항상 선택한다
  - 그렇지 않고 Tomcat 풀링 `DataSource`를 사용할 수 있는 경우 이를 사용한다
  - 그렇지 않으면 Commons DBCP2를 사용한다
  - HikariCP, Tomcat, DBCP2 중 어느것도 사용할 수 없으면 Oracle UCP를 사용한다
  - 이 알고리즘을 완전히 우회하고 사용할 연결 풀을 지정할 수 있다
- `spring-boot-starter-jdbc` 또는 `spring-boot-starter-data-jpa`를 사용하면 자동으로 HikariCP가 사용된다

## HikariCP
- HikariCP는 안정적인 고성능 JDBC 커넥션 풀
- 다른 연결 풀 API에 비해 훨씬 빠르고 가벼우며 더 나은 성능을 제공한다
- Spring Boot 2의 기본 DataSource 구현이다
- 속성
  - `allow-pool-suspension` : 
  - `auto-commit` : 기본 자동 커밋 동작
  - `catalog` : 
  - `connection-init-sql` : 
  - `connection-init-sql` : 
  - `connection-timeout` : 클라이언트가 연결을 기다리는 최대 시간(밀리초)
  - `data-source-class-name` : 
  - `data-source-j-n-d-i` : 
  - `data-source-properties` :
  - `driver-class-name` :
  - `exception-override-class-name` : 
  - `health-check-properties` : 
  - `health-check-registry` : 
  - `idle-timeout` : 연결을 위한 최대 유휴 시간
  - `initialization-fail-timeout` 
  - `isolate-internal-queries`
  - `jdbc-url`
  - `keepalive-time` 
  - `leak-detection-threshold`
  - `login-timeout`
  - `max-lifetime` : 닫힌 후 풀에서 연결의 최대 수명(밀리초)
  - `maximum-pool-size` : 최대 풀 크기
  - `metric-registry`
  - `metrics-tracker-factory`
  - `minimum-idle` : 연결 풀에서 HikariCP가 유지 관리하는 최소 유휴 연결 수
  - `password`
  - `pool-name`
  - `read-only`
  - `register-mbeans`
  - `scheduled-executor`
  - `schema`
  - `transaction-isolation`
  - `username`
  - `validation-timeout` : 연결이 활성 상태인지 테스트되는 최대 시간을 제어. `connection-timeout` 값보다 작어야한다 (Default : 5000, Min : 250ms)

<br/>

---
 
<br/>

참고 및 출처
- [Configuring Hikari Connection Pool with Spring Boot](https://www.javadevjournal.com/spring-boot/spring-boot-hikari/)
- [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/2.6.2/reference/htmlsingle/)
- [HikariCP](https://github.com/brettwooldridge/HikariCP)