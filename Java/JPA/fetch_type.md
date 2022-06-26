- [즉시 로딩, 지연 로딩](#즉시-로딩,-지연-로딩)
  - [지연 로딩](#지연-로딩)
  - [즉시 로딩](#즉시-로딩)
    - [즉시 로딩 주의](#즉시-로딩-주의)

# 즉시 로딩, 지연 로딩

Member를 조회할 때 Team도 함께 조회해야 할까?

## 지연 로딩

단순히 Member 정보만 조회하는 비즈니스 로직이라면?

`FetchType.LAZY`

가 붙어있는 필드에 대해 프록시 객체로 조회한다

```java
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "TEAM_ID")
private Team team;
```

```java
Member m = em.find(Member.class, member1.getId());
System.out.println("m = " + m.getTeam().getClass());
// m = class hellojpa.Team$HibernateProxy$yChKvCrZ
// Team은 프록시 객체인 상태임

m.getTeam().getName(); // Team의 값에 접근하는 시점에 쿼리가 나감
```

- 네트워크를 2번 탐

<br/>

## 즉시 로딩

Member와 Team을 자주 함께 사용한다면?

`FetchType.EAGER`

를 써서 함께 조회

```java
@ManyToOne(fetch = FetchType.EAGER)
@JoinColumn(name = "TEAM_ID")
private Team team;
```

```java
Member m = em.find(Member.class, member1.getId()); // Member, Team을 조인해서 가져옴
System.out.println("m = " + m.getTeam().getClass());
// m = class hellojpa.Team

m.getTeam().getName();
```

JPA의 구현체 마다 EAGER를 처리하는 방법이 다를 수 있다

예를 들어, 어떤 구현체는 쿼리를 두 번 실행해서 Member를 조회하고, Team을 조회할 수 있다

그리고 다른 구현체는 Member와 Team을 조인해서 조회할 수 있는 것이다

→ JPA 구현체는 가능하면 조인을 사용해서 SQL 한번에 함께 조회하도록 구현되어 있다

### 즉시 로딩 주의

실무에서 가급적 지연 로딩만 사용

- 즉시 로딩을 적용하면 예상하지 못한 SQL이 발생한다

    ```java
    Member m = em.find(Member.class, member1.getId());
    ```

    - 딱, 이 부분만 보면 Member만 찾아오는거 같기 때문에 Member에 EAGER로 설정된 모든 테이블이 조인될거라 예상하지 못할 수 있다
    - 만약 EAGER로 설정된 테이블이 10개 이상이라면 ?? → 엄청 큰 쿼리가 나가게 되는 것이다
- 즉시 로딩은 JPQL에서 N + 1 문제를 일으킨다

    ```java
    // 전체 Member 조회
    List<Member> members = em.createQuery("select m from Member m", Member.class)
                        .getResultList();
    ```

    ```
    Hibernate:
        /* select
                m
        from
            Member m */ select
                member0_.MEMBER_ID as 
                ...
            from
                Member member0_
    
    Hibernamte:
        select
            team0_.TEAM_ID ad
            ...
        from 
            Team team0_
        where
            team0_.TEAM_ID=?
    ```

    - 이렇게 쿼리가 더 나간다 → EAGER인데 왜 쿼리가 여러번 나가지 ?
    - JPQL은 쿼리가 그대로 SQL로 번역이 되어서 나간다 → 그러면 당연히 Member만 SELECT 한다
    - Member를 다 가지고 와 봤더니 → Team이 EAGER로 되어있네?
        - Member로 조회된 갯수 만큼 쿼리가 또 나간다
    - N + 1 은 → 처음 쿼리가 1개 나가고 추가 쿼리가 N가 나간다는 뜻이다
    - 해결 방법

      일단 다 지연 로딩으로 깐다

        1. 패치 조인을 사용한다

           런타임에 동적으로 원하는 쿼리를 선택해서 한번에 가져오는 것

        2. `@Entitygraph` 라는 어노테이션을 사용
        3. batch size 를 사용

           1 + 1 으로 쿼리가 나감

- `@ManyToOne`, `@OneToOne` 은 기본이 즉시 로딩 → 따라서 LAZY로 설정해줘야 된다
- `@OneToMany`, `@ManyToMany` 는 기본이 지연 로딩

<br/>

--- 

<br/>

참고 및 출처
- [자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)