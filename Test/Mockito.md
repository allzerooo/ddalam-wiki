- [Mockito](#mockito)
  - [Mock을 사용하는 경우](#mock을-사용하는-경우)
  - [Mock을 만드는 방법](#mock을-만드는-방법)
    - [`Mockito.mock()` 메서드로 만드는 방법](#mockitomock-메서드로-만드는-방법)
    - [`@Mock` 애노테이션으로 만드는 방법](#mock-애노테이션으로-만드는-방법)
  - [Mock이 어떻게 동작해야 하는지 관리하는 방법 (Stubbing)](#mock이-어떻게-동작해야-하는지-관리하는-방법-stubbing)
  - [Mock 객체 확인](#mock-객체-확인)
  - [BDD 스타일 Mockito API](#bdd-스타일-mockito-api)
    - [BDD(Behavior Driven Development)](#bddbehavior-driven-development)
    - [BddMockito](#bddmockito)

# Mockito
- Mock : 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체
- Mockito : Mock 프레임워크. Mock 객체를 쉽게 만들고 관리하고 검증할 수 있는 방법을 제공한다
- `spring-boot-stater-test`에 내장되어 있음

## Mock을 사용하는 경우
- 인터페이스에 의존해서 개발할 때 구현체가 준비가 안됐거나
- 구현체가 있더라도 구현체와 상관없이 테스트하고 싶을 때

## Mock을 만드는 방법

### `Mockito.mock()` 메서드로 만드는 방법
```java
MemberService memberService = mock(MemberService.class);
StudyRepository studyRepository = mock(StudyRepository.class);
```

### `@Mock` 애노테이션으로 만드는 방법
- 여러 테스트 메서드에서 사용할 때
  ```java
  @ExtendWith(MockitoExtension.class)
  class StudyServiceTest {

    @Mock
    StudyRepository studentRepository;

    @Test
    void createStudy_2() {
      StudyService studyService = new StudyService(studentRepository);

      assertNotNull(studyService);
    }

  }
  ```
  - JUnit5 extension으로 `MockitoExtension`을 사용해야 한다
- 특정 메서드에서만 사용하고 싶을 때
  ```java
  @ExtendWith(MockitoExtension.class)
  class StudyServiceTest {

    @Test
    void createStudy_3(@Mock StudyRepository studentRepository) {
      StudyService studyService = new StudyService(studentRepository);

      assertNotNull(studyService);
    }

  }
  ```
  - JUnit5 extension으로 `MockitoExtension`을 사용해야 한다

## Mock이 어떻게 동작해야 하는지 관리하는 방법 (Stubbing)
Mock 객체가 어떤 행동을 해줬으면 좋겠다 → 그렇게 동작하도록 만드는 것 → Stubbing

- 모든 Mock 객체의 기본 행동
  - return 값이 있는 메서드는 null을 리턴한다 (Optional 타입은 Optional.empty 리턴)
  - Primitive 타입의 필드가 있다면 기본 Primitive 값을 따른다
  - 콜렉션은 비어있는 콜렉션
  - void 메서드는 예외를 던지지 않고 아무런 일도 발생하지 않는다

Mock 객체를 조작해서 Mock 객체의 행동을 변경할 수 있다
- 특정한 매개변수를 받은 경우 특정한 값을 리턴하거나 예외를 던지도록 만들 수 있다
  - [How about some stubbing?](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#2)
  - [Argument matchers](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#3)
  ```java
  Member member = new Member();
  member.setId(1L);
  member.setEmail("abcd@email.com");

  when(memberService.findById(1L)).thenReturn(Optional.of(member));   // stubbing
  // when(memberService.findById(any())).thenReturn(Optional.of(member));   // any() 이런 것도 가능

  Optional<Member> findById = memberService.findById(1L);   // memberService.findById()가 1L로 호출되었기 때문에 위 stubbing에 의해 member가 리턴됨
  assertEquals("abcd@email.com", findById.get().getEmail());

  ```
- void 메소드 특정 매개변수를 받거나 호출된 경우 예외를 발생 시킬 수 있다
  - [Subbing void methods with exceptions](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#5)
  ```java
  doThrow(new IllegalArgumentException()).when(memberService).validate(1L);  // validate -> MemberService의 메서드임
  memberService.validate(1L); // 예외가 발생함
  assertThrows(IllegalArgumentException.class, () -> {
    memberService.validate(1L);
  });
  memberService.validate(2L); // 예외가 발생하지 않음
  ```
- 메소드가 동일한 매개변수로 여러번 호출될 때 각기 다르게 행하도록 조작할 수도 있다
  - [Stubbing consecutive calls](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#10)
  ```java
  when(memberService.findById(any()))
          .thenReturn(Optional.of(member))  // 처음 호출 됐을 때
          .thenThrow(new RuntimeException())  // 두번 째 호출 됐을 떼
          .thenReturn(Optional.empty());  // 세번 째 호출 됐을 때

  Optional<Member> findById = memberService.findById(1L); // 처음 호출 됐을 때 member가 리턴

  assertThrows(IllegalArgumentException.class, () -> {  // 두번 째 호출 됐을 떼 예외가 리턴
    memberService.findById(2L);
  });

  assertEquals(Optional.empty(), memberService.findById(3L)); // 세번 째 호출 됐을 때 empty가 리턴

  ```


## Mock 객체 확인
Mock 객체가 어떻게 사용이 됐는지 확인할 수 있다
- 특정 메소드가 특정 매개변수로 몇번 호출 되었는지, 최소 한번은 호출 됐는지, 전혀 호출되지 않았는지
  - [Verifying exact number of invocations](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#exact_verification)
  ```java
  Study study = new Study(10, "테스트");
  studyService.createNewStudy(1L, study); // 여기에서 memberService.notify()를 호출

  verify(memberService, times(1)).notify(study);  // study 대신 any() 이런 것도 가능 

  verify(memberService, never()).validate(any());
  ```
- 어떤 순서대로 호출했는지
  - [Verification in order](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#in_order_verification)
  ```java
  InOrder inOrder = inOrder(memberService);
  inOrder.verify(memberService).notify(study);  // study로 notify가 먼저 호출되고
  inOrder.verify(memberService).notify(member); // member로 notify가 호출되는지

  verifyNoMoreInteractions(memberService); // member로 notify가 호출되고 더 이상 아무 액션도 일어나지 않아야 한다
  ```
- 특정 시간 이내에 호출됐는지
  - [Verification with timeout](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#verification_timeout)
- 특정 시점 이후에 아무 일도 벌어지지 않았는지
  - [Finding redundant invocations](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#finding_redundant_invocations)

## BDD 스타일 Mockito API

### BDD(Behavior Driven Development)
- 애플리케이션이 어떻게 “행동”해야 하는지에 대한 공통된 이해를 구성하는 방법으로, TDD에서 창안
- 행동에 대한 스팩
  - Title
  - Narrative
    - As a / I want / so that
  - Acceptance criteria
    - Given / When / Then

### BddMockito
Mockito는 BddMockito라는 클래스를 통해 BDD 스타일의 API를 제공한다

- `when` → `given`
  ```java
  given(memberService.findById(1L)).willReturn(Optional.of(member));
  given(studyRepository.save(study)).willReturn(study);
  ```
- `verify` → `then`
  ```java
  then(memberService).should(times(1)).notify(study);
  then(memberService).shouldHaveNoMoreInteractions();
  ```

<br/>

---

<br/>

출처 및 참고
- [더 자바, 애플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/the-java-application-test/dashboard)