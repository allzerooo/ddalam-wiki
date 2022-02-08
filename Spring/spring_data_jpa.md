- [Spring Data JPA](#spring-data-jpa)
  - [ORM, JPA, JPQL](#orm-jpa-jpql)
    - [ORM (Object Relational Mapping)](#orm-object-relational-mapping)
    - [JPA (Jakarta(Java) Persistence API)](#jpa-jakartajava-persistence-api)
      - [Persistence (영속성)](#persistence-영속성)
    - [JPQL (Jakarta(Java) Persistence Query Language)](#jpql-jakartajava-persistence-query-language)
  - [Hibernate vs. Spring Data JPA](#hibernate-vs-spring-data-jpa)
    - [Hibernate](#hibernate)
      - [Hibernate Query Language](#hibernate-query-language)
      - [Criteria Query](#criteria-query)
      - [Native SQL Query](#native-sql-query)
    - [Spring Data JPA](#spring-data-jpa-1)
      - [Spring Data JPA를 사용하면](#spring-data-jpa를-사용하면)
  - [in memory 테스트 DB - H2](#in-memory-테스트-db---h2)

# Spring Data JPA

## ORM, JPA, JPQL

### ORM (Object Relational Mapping)
객체 지향 언어를 이용하여, 서로 호환되지 않는 타입 간의 데이터를 변환하는 기술
- 좁은 의미 : DB(RDBMS) 테이블 데이터를 (자바) 객체와 매핑하는 기술
- 효과 : RDBMS를 객체 지향 DB로 가상화하는 것
- ORM으로 얻고자 하는 것
  - DB의 추상화 : 특정 DB에 종속된 표현(ex: SQL)이나 구현이 사라지고, DB 변경에 좀 더 유연해짐
    - ORM을 사용하지 않았을 때는 구체적으로 어떤 DB에 접속하는지 알고 개발(해당 DB에 접속, DB 쿼리 수행, 자원 반납 등) → 하고자 하는건 데이터를 조작하는 것인데 이 외에 해야되는 작업들이 추가적으로 필요함 → 데이터를 조작하는 것 외의 부분들을 추상화하고, 프레임워크에게 위임함으로써 비즈니스 로직에 집중할 수 있다, 코드에 어떤 DB를 사용하는지 구체적인 표현을 사용하지 않게됨(인터페이스에 의존, 구현은 프래임워크에게 위임)
  - 객체의 이점을 활용 : 객체간 참조, type-safety
  - 관심사분리 : DB 동작에 관한 코드 작성의 반복을 최소화하고 비즈니스 로직에 집중

### JPA (Jakarta(Java) Persistence API)
자바에서 ORM 기술을 사용해 RDBMS를 다루기 위한 인터페이스 표준 명세
- 구성
  - core API
  - JPQL
  - metadata (object와 relation을 표현하는) 
  - (+ Criteria API)
- 기본적으로 관계형 데이터베이스의 영속성(persistence)만을 규정
  - JPA 구현체 중에 다른 유형의 데이터베이스 모델을 지원하는 경우가 있지만, 원래 JPA 스펙과는 무관

#### Persistence (영속성)
프로세스가 만든 시스템의 상태가 종료된 후에도 사라지지 않는 특성
- 구현 방법 : 시스템의 상태를 데이터 저장소에 데이터로 저장한다
  - 사라지는 데이터 - 주기억장치(휘발성 스토리지)에 저장된 데이터
    - 프로세스 메모리 안의 데이터 (변수, 상수, 객체, 함수 등)
  - 사라지지 않는 데이터 - 보조기억장치(비휘발성 스토리지)에 저장된 데이터
    - 하드디스크, SSD에 기록된 데이터 (파일, 데이터베이스 등)
- 영속성 프레임워크 : 영속성을 관리하는 부분을 persistence layer로 추상화하고, 이를 전담하는 프
레임워크에게 관리를 위임
- JPA에서 persistence란 : 프로세스가 DB로부터 읽거나 DB에 저장한 정보의 특성

### JPQL (Jakarta(Java) Persistence Query Language)
플랫폼(RDBMS)으로부터 독립적인 객체 지향 쿼리 언어
- JPA 표준의 일부로 정의됨
- RDBMS의 엔티티(Entity)를 다루는 쿼리를 만드는데 사용
- SQL의 영향을 받아서 형식이 매우 유사
- SQL과 JPQL은 다른 언어이다
  - SQL : 표준 ANSI SQL을 기준으로 만든, 특정 DB에 종속적인 언어
  - JPQL : 특정 DB에 종속적인 언어가 아님
- JPA 프레임워크를 사용한다면
  - 특별한 요구사항이 있지 않은 한, JPQL을 몰라도 된다
  - JPQL을 직접 코드에서 사용하고 있다면, 반드시 필요했던 일인지 검토하기

## Hibernate vs. Spring Data JPA

### Hibernate
"MORE THAN AN ORM, DISCOVER THE HIBERNATE GALAXY."
- 자바 생태계를 대표하는 ORM framework
- 스프링 부트에서 채택한 메인 ORM framework
- JPA 표준 스펙을 구현한 JPA Provider
- 고성능, 확장성, 안정성을 표방
- 다양한 하위 제품들로 나뉨
  - Hibernate ORM
  - Hibernate Validator
  - Hibernate Reactive

#### Hibernate Query Language
하이버네이트가 사용하는 SQL 스타일 비표준 쿼리 언어
- 객체 모델에 초점을 맞춰 설계됨
- JPQL의 바탕이 됨(JPQL은 HQL의 subset)
  - JPQL은 완벽한 HQL 문장이지만, 반대로는 성립하지 않음
  
#### Criteria Query
type-safety를 제공하는 JPQL의 대안 표현법

#### Native SQL Query
특정 DB에 정속된 SQL도 사용 가능

### Spring Data JPA
스프링에서 제공하는 JPA 추상화 모듈
- JPA 구현체의 사용을 한 번 더, Repository 라는 개념으로 추상화
- JPA 구현체의 사용을 감추고, 다양한 지원과 설정 방법을 제공
- JPA 기본 구현체로 Hibernate 사용
- Querydsl 지원

#### Spring Data JPA를 사용하면
JPA, 즉 하이버네이트 구현체를 몰라도 되어야 한다
- EntityManager를 직접 사용하지 않는다
- JPQL을 직접 사용하지 않는다
- persist(), merge(), close() 를 직접 사용하지 않는다
- 트랜잭션을 getTransaction(), commit(), rollback() 으로 관리하지 않는다
- 코드가 하이버네이트를 직접 사용하고 있다면
  - 꼭 필요한 코드인지, 아니면 Spring Data JPA로 할 수 있는 일인지 확인
  - 그 코드는 하이버네이트와 직접적인 연관 관계를 가지게 됨
  - 추상화의 이점을 포기하게 되는 셈

## in memory 테스트 DB - H2
"Java SQL database"
- 스프링 부트가 지원하는, 가장 세팅하기 편한 인메모리 DB
- 빠르다, 오픈소스, JDBC API
- 다양한 모드 지원 : embedded, server, in-memory
- 브라우저 콘솔 지원 (h2-console)
- 경량jar : 약 2MB
- 순수 자바로 구현
- Compatibility mode : IBM DB2, Derby, HSQLDB, MSSQL, MySQL, Oracle, PostgreSQL

<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)