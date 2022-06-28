
# 선언적 트랜잭션
`@Transactional` 을 통한 선언적 트랜잭션 관리 방식을 사용하게 되면 기본적으로 프록시 방식의 AOP가 적용된다

프록시를 사용하지 않으면 → 서비스 계층의 비즈니스 로직에서 트랜잭션을 직접 시작해야 된다

```java
//트랜잭션 시작
TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());

try {
	//비즈니스 로직
	bizLogic(fromId, toId, money); transactionManager.commit(status); //성공시 커밋
} catch (Exception e) { 
	transactionManager.rollback(status); //실패시 롤백
  throw new IllegalStateException(e);
}
```

프록시를 사용하면 → 트랜잭션을 처리하는 객체와 비즈니스 로직을 처리하는 서비스 객체를 명확하게 분리할 수 있다

<p align="center">
    <img src="../../image/spring_transaction_proxy.png"  width="600" height="auto">
</p>

트랜잭션 프록시가 트랜잭션을 시작한 후에 서비스를 대신 호출한다. 트랜잭션 프록시 덕분에 서비스 계층에는 순수한 비즈니스 로직만 남길 수 있다

## 트랜잭션 AOP

스프링의 트랜잭션 AOP는 `@Transactional` 애노테이션을 인식해서 트랜잭션을 처리하는 프록시를 적용해준다

<br/>

---

<br/>

출처 및 참고
- [스프링 DB 2편 - 데이터 접근 활용 기술(김영한)](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-db-2/dashboard)