- [JPA 설정하기](#jpa-설정하기)
  - [표준 위치](#표준-위치)
  - [`persistence.xml`](#persistencexml)

# JPA 설정하기

## 표준 위치
`/resources/META-INF/persistence.xml`

## `persistence.xml`
- `persistence version="X.X"` : JPA 버전
- `persistence-unit name="jpa"` : 이름 지정, 보통 데이터베이스 당 1개 생성
- `javax.persistence`로 시작하는 property : JPA 표준 속성
- `hibernate`로 시작하는 property : hibernate 전용 속성
- `hibernate.dialect` : [데이터베이스 방언]() 지정

```xml
<!-- 필수 속성 -->
<property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
<property name="javax.persistence.jdbc.user" value="sa"/>
<property name="javax.persistence.jdbc.password" value=""/>
<property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
<property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
```

<br/>

--- 

<br/>

출처
- [자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)