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

  - 같은 타입이 둘 이상이면 예외 발생
    ```java
    @Test
    @DisplayName("같은 타입이 둘 이상 있으면 예외가 발생한다")
    void findBeanByTypeDuplicate() {
        assertThrows(NoUniqueBeanDefinitionException.class,
                () -> ac.getBean(MemberRepository.class));
    }
    ```
  - 예외 해결 : 같은 타입이 둘 이상이면 빈 이름을 지정
    ```java
    @Test
    @DisplayName("같은 타입이 둘 이상 있으면 빈 이름을 지정하면 된다")
    void findBeanByName() {
        MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
        assertThat(memberRepository).isInstanceOf(MemberRepository.class);
    }
    ```

- 특정 타입을 모두 조회

  ```java
  @Test
  @DisplayName("특정 타입을 모두 조회하기")
  void findAllBeanByType() {
      Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
      assertThat(beansOfType.size()).isEqualTo(2);
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

- 부모 타입으로 조회하면 자식 타입도 함께 조회된다
  ```java
  @Test
  @DisplayName("부모 타입으로 모두 조회하기")
  void findAllByParentType() {
      Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
      assertThat(beansOfType.size()).isEqualTo(2);
  }
  ```
  ```java
  @Test
  @DisplayName("부모 타입으로 모두 조회하기 - Object")
  void findAllBeanByObjectType() {
      Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
      for (String key : beansOfType.keySet()) {
          System.out.println("key = " + key + " value=" + beansOfType.get(key));
      }
  }
  ```

<br/>

[출처]

- 인프런 스프링 핵심 원리 기본편 (김영한)
