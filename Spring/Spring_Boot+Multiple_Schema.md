## <a name="Spring_Boot+Multiple_Schema"></a>Spring Boot + Multiple Schema

### 문제

한 프로젝트에서 여러 스키마를 사용해야 되는 경우가 있습니다. 하나의 스키마를 사용할 때는 datasource 설정 시 url에 사용할 스키마를 명시하는데, 여러 스키마를 사용할 때는 특정 스키마만을 명시하기 어렵습니다.
따라서, application.yml의 datasource 부분은 아래와 같이 설정했습니다.

```yaml
spring:
  ...
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: 'jdbc:mariadb://AWS-RDS-END-URL:PORT/?useUnicode=true&allowMultiQueries=true'
    username: USER_NAME
    password: PASSWORD
  ...
```

<br/>

datasource의 url 속성에서 `PORT/` 뒤에 프로젝트에서 사용할 스키마를 명시하지 않으면 엔티티 매핑 시 `@Table` 어노테이션의 `schema` 속성을 사용해도 데이터베이스의 특정 스키마와 매핑할 수 없습니다.

```java
@Table(name = "User", schema = "Shop")
public class User { ... }
```

위와 같이 Shop 스키마의 User 테이블을 매핑하려고 하면 `java.sql.SQLException: No database selected` 에러가 발생합니다. 

<br/>

### 해결 방법

저는 domain 디렉토리 아래 스키마에 해당하는 디렉토리를 생성하고, 각 스키마 디렉토리 마다 datasource config 파일을 추가, 각 스키마에 포함된 테이블은 해당 디렉토리 아래에서 매핑하는 방법을 사용했습니다.

<center><img src="https://user-images.githubusercontent.com/7088950/81043342-09247900-8eed-11ea-9f8b-b7a0e114dd1a.png" width="300"></center>

<br/>

여러 개의 스키마를 사용하려면 우선 application.yml에 datasource를 추가해야 합니다.

```yaml
spring:
  ...

schema1:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: 'jdbc:mariadb://AWS-RDS-END-URL:PORT/Schema1?useUnicode=true&allowMultiQueries=true'
    username: USER_NAME
    password: PASSWORD

schema2:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: 'jdbc:mariadb://AWS-RDS-END-URL:PORT/Schema2?useUnicode=true&allowMultiQueries=true'
    username: USER_NAME
    password: PASSWORD
```

`schema1`, `schema2`는 원하는 이름으로 지정하고, url 속성의 `PORT/` 뒤에 사용할 스키마를 명시해 줍니다. 저는 예를 들기 위해 편의상 schema1, schema2를 사용했습니다. 실무에서는 사용할 schema 명과 모두 일치시켰습니다.

<br/>

이제 각 스키마를 사용할 디렉토리 밑에 config 파일을 추가합니다. 저는 domain/schema1/Schema1DataSourceConfig 와 같이 만들었습니다. 그리고 config 파일에 repository의 `basePackages` 경로를 지정해줘야 되기 때문에 각 스키마 디렉토리 아래 JPA repository를 위한 디렉토리를 추가해주세요.

<br/>

Schema1DataSourceConfig 파일은 아래와 같습니다.

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
    entityManagerFactoryRef = "schema1EntityManagerFactory",
    // PATH에는 domain에 이르는 path를 적어주세요. 생략을 위해 PATH라고 적었습니다.
    // 저는 schema1 디렉토리 아래에 "repository"라는 이름으로 repository 디렉토리를 추가해줬습니다.
    basePackages = {"PATH.domain.schema1.repository"})
public class Doctalk2016DataSourceConfig {

    @Autowired
    private Environment env;

    @Primary
    @Bean(name = "schema1DataSource")
    @ConfigurationProperties(prefix = "schema1.datasource")
    public DataSource schema1DataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(env.getProperty("schema1.datasource.driver-class-name"));
        dataSource.setUrl(env.getProperty("schema1.datasource.url"));
        dataSource.setUsername(env.getProperty("schema1.datasource.username"));
        dataSource.setPassword(env.getProperty("schema1.datasource.password"));
        return dataSource;
    }

    @Primary
    @Bean(name = "schema1EntityManagerFactory")
    public LocalContainerEntityManagerFactoryBean schema1EntityManagerFactory(
            EntityManagerFactoryBuilder builder, @Qualifier("schema1DataSource") DataSource schema1DataSource) {
        return builder
                .dataSource(schema1DataSource)
                .packages("PATH.domain")    // PATH에는 domain에 이르는 path를 적어주세요
                .persistenceUnit("schema1")
                .build();
    }

    @Primary
    @Bean(name = "schema1TransactionManager")
    public PlatformTransactionManager schema1TransactionManager(
            @Qualifier("schema1EntityManagerFactory") EntityManagerFactory schema1EntityManagerFactory) {
        return new JpaTransactionManager(schema1EntityManagerFactory);
    }

}
```

<br/>

Schema2DataSourceConfig 파일은 아래와 같습니다. Schema1DataSourceConfig 파일과 다른 점은 `@Primary` 어노테이션의 여부 입니다. default로 사용할 스키마에 `@Primary`를 적용해 주세요.

```java
@Configuration
@EnableTransactionManagement
@EnableJpaRepositories(
        entityManagerFactoryRef = "schema2EntityManagerFactory",
        // PATH에는 domain에 이르는 path를 적어주세요
        basePackages = {"PATH.domain.schema2.repository"})
public class Doctalk2016DataSourceConfig {

    @Autowired
    private Environment env;

    @Bean(name = "schema2DataSource")
    @ConfigurationProperties(prefix = "schema2.datasource")
    public DataSource schema2DataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(env.getProperty("schema2.datasource.driver-class-name"));
        dataSource.setUrl(env.getProperty("schema2.datasource.url"));
        dataSource.setUsername(env.getProperty("schema2.datasource.username"));
        dataSource.setPassword(env.getProperty("schema2.datasource.password"));
        return dataSource;
    }

    @Bean(name = "schema2EntityManagerFactory")
    public LocalContainerEntityManagerFactoryBean schema2EntityManagerFactory(
            EntityManagerFactoryBuilder builder, @Qualifier("schema2DataSource") DataSource schema2DataSource) {
        return builder
                .dataSource(schema2DataSource)
                .packages("PATH.domain")    // PATH에는 domain에 이르는 path를 적어주세요
                .persistenceUnit("schema2")
                .build();
    }

    @Bean(name = "schema2TransactionManager")
    public PlatformTransactionManager schema2TransactionManager(
            @Qualifier("schema2EntityManagerFactory") EntityManagerFactory schema2EntityManagerFactory) {
        return new JpaTransactionManager(schema2EntityManagerFactory);
    }

}
```

<br/>

마지막으로, repository 구현 시 주의해야 할 점이 있습니다.  `EntityManager`를 선언할 때 어떤 `EntityManagerFactory`를 사용할 것인지 명시해줘야 합니다. 아래 코드 중 Table1RepositoryImpl의 생성자에서 `@Qualifier("schema1EntityManagerFactory")`를 사용해 `EntityManagerFactory`를 명시해 줬습니다. `@Qualifier`에 들어가는 값은 Schema1DataSourceConfig에서 사용한 Bean name과 일치해야 됩니다.

```java
@Primary
@Bean(name = "schema1EntityManagerFactory")
public LocalContainerEntityManagerFactoryBean schema1EntityManagerFactory(
        EntityManagerFactoryBuilder builder, @Qualifier("schema1DataSource") DataSource schema1DataSource) {
    return builder
            .dataSource(schema1DataSource)
            .packages("PATH.domain")
            .persistenceUnit("schema1")
            .build();
}
```

<br/>

저는 querydsl을 사용했고, schema1/repository 디렉토리 아래 Table1Repository, Table1RepositoryCustom, Table1RepositoryImpl과 같이 파일을 나누어 개발했습니다. schema2에 포함된 테이블을 다룰 때도 동일한 방법을 적용하면 됩니다.

<br/>

repository 디렉토리 아래 있는 파일들의 예 입니다.

```java
@Repository
public interface Table1Repository extends JpaRepository<Table1, Integer>, Table1RepositoryCustom {
	...
}
```

```java
public interface Table1RepositoryCustom {

    List<UserDto> retrieveUser();

    List<String> tagList(String postId);

    Integer test();

	...
}
```

```java
public class Table1RepositoryImpl implements Table1RepositoryCustom {

    private final JPAQueryFactory queryFactory;

    public Table1RepositoryImpl(@Qualifier("schema1EntityManagerFactory") EntityManager em) {
        this.queryFactory = new JPAQueryFactory(em);
    }

    public List<UserDto> retrieveUser() {
        ...
    }

    public List<String> tagList(String postId) {
        ...
    }

    public Integer test() {
        ...
    }

}
```