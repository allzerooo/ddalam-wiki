- [트랜잭션](#트랜잭션)
    - [트랜잭션 추상화](#트랜잭션-추상화)
        - [PlatformTransactionManager](#platformtransactionmanager)

# 트랜잭션

## 트랜잭션 추상화

데이터 접근 구현 기술마다 트랜잭션을 사용하는 방법이 다르다
- JDBC : `con.setAutoCommit(false)`
- JPA : `transaction.begin()`

데이터 접근 기술이 변경되었을 때 서비스 계층의 트랜잭션 코드도 함께 수정되지 않게 하려면 트랜잭션 기능을 추상화해야 된다

### `PlatformTransactionManager`
<p align="center">
    <img src="../image/spring_transaction_abstraction.png"  width="480" height="auto">
</p>

- 스프링은 이런 고민을 위해 트랜잭션 추상화를 제공한다
- 데이터 접근 기술에 따른 트랜잭션의 구현체도 대부분 만들어두어서 가져다 사용하기만 하면 된다
- 스프링 트랜잭션 추상화의 핵심은 `PlatformTransactionManager` 인터페이스이다

```java
package org.springframework.transaction;

public interface PlatformTransactionManager extends TransactionManager {
  TransactionStatus getTransaction(@Nullable TransactionDefinition definition)
          throws TransactionException;
  void commit(TransactionStatus status) throws TransactionException;
  void rollback(TransactionStatus status) throws TransactionException;
}
```

- `getTransaction()` : 트랜잭션을 시작한다
    - 이름이 `getTransaction()` 인 이유는 기존에 이미 진행중인 트랜잭션이 있는 경우 해당 트랜잭션에 참여할 수 있기 때문이다
- `commit()` : 트랜잭션을 커밋한다
- `rollback()` : 트랜잭션을 롤백한다

<br/>

---

<br/>

출처 및 참고
- [스프링 DB 1편 - 데이터 접근 핵심 원리(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-1/dashboard)