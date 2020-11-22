# 스프링 빈 조회

- `annotationConfigApplicationContext.getBean(빈이름, 타입)`

  ```java
  @Test
  @DisplayName("빈 이름으로 조회")
  void findBeanByName() {
      MemberService memberService = ac.getBean("memberService", MemberService.class);
      assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }
  ```

- `annotationConfigApplicationContext.getBean(타입)`

  ```java
  @Test
  @DisplayName("타입으로 조회")
  void findBeanByType() {
      MemberService memberService = ac.getBean(MemberService.class);
      assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
  }
  ```

- 조회하려는 빈이 없을 때 예외

  - `NoSuchBeanDefinitionException: No bean named 'xxxxx' available`

    ```java
    @Test
    @DisplayName("빈 이름으로 조회 실패")
    void findBeanByNameX() {
        assertThrows(NoSuchBeanDefinitionException.class,
                () -> ac.getBean("xxxx", MemberService.class));
    }
    ```

<br/>

[출처]

- 인프런 스프링 핵심 원리 기본편 (김영한)
