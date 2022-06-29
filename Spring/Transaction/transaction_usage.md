- [트랜잭션 사용 방법](#트랜잭션-사용-방법)
  - [선언적 트랜잭션 관리](#선언적-트랜잭션-관리)
  - [프로그래밍 방식 트랜잭션 관리](#프로그래밍-방식-트랜잭션-관리)
  - [트랜잭션이 적용되고 있는지 확인하는 방법](#트랜잭션이-적용되고-있는지-확인하는-방법)

# 트랜잭션 사용 방법
`PlatformTransactionManger` 를 사용하는 방법은 크게 2가지가 있다

## 선언적 트랜잭션 관리

`@Transactional` 애노테이션 하나만 선언해서 편리하게 트랜잭션을 적용하는 것

## 프로그래밍 방식 트랜잭션 관리

트랜잭션 관련 코드를 직접 작성하는 것

## 트랜잭션이 적용되고 있는지 확인하는 방법

```java
boolean txActive = TransactionSynchronizationManager.isActualTransactionActive();
```

true인지 false인지 확인할 수 있다

<br/>

---

<br/>

출처 및 참고
- [스프링 DB 2편 - 데이터 접근 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2/dashboard)