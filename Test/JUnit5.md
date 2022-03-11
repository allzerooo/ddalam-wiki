- [JUnit 5](#junit-5)
  - [`@ExtendWith()`](#extendwith)
  - [`@WebMvcTest()`](#webmvctest)
    - [`MockMvc`](#mockmvc)
  - [Mockito](#mockito)
    - [Mock을 사용하는 경우](#mock을-사용하는-경우)
    - [Mock을 만드는 방법](#mock을-만드는-방법)
      - [`Mockito.mock()` 메서드로 만드는 방법](#mockitomock-메서드로-만드는-방법)
      - [`@Mock` 애노테이션으로 만드는 방법](#mock-애노테이션으로-만드는-방법)
    - [Mock이 어떻게 동작해야 하는지 관리하는 방법](#mock이-어떻게-동작해야-하는지-관리하는-방법)
    - [Mock의 행동을 검증하는 방법](#mock의-행동을-검증하는-방법)
    - [`@InjectMocks`](#injectmocks)
    - [`@MockBean`](#mockbean)
  - [Controller 테스트](#controller-테스트)
  - [Service 테스트](#service-테스트)
  - [테스트 순서](#테스트-순서)

# JUnit 5
Java의 Unit 테스트를 위한 프래임워크

## `@ExtendWith()`
외부 기능을 사용해 테스트
```java
@ExtendWith(MockitoExtension.class)
```
- Mockito라는 외부 기능을 사용해 테스트하겠다고 선언

## `@WebMvcTest()`
원하는 컨트롤러 관련 빈들만 등록해서 테스트 가능
```java
@WebMvcTest(MemberController.class)
```

### `MockMvc`
컨트롤러 메서드 호출을 만들 수 있다
```java
@Autowired
private MockMvc mockMvc;
```

## Mockito
- Mock : 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체
- Mockito : Mock 프레임워크. Mock 객체를 쉽게 만들고 관리하고 검증할 수 있는 방법을 제공한다
- `spring-boot-stater-test`에 내장되어 있음

### Mock을 사용하는 경우
- 인터페이스에 의존해서 개발할 때 구현체가 준비가 안됐거나
- 구현체가 있더라도 구현체와 상관없이 테스트하고 싶을 때

### Mock을 만드는 방법

#### `Mockito.mock()` 메서드로 만드는 방법
```java
MemberService memberService = mock(MemberService.class);
StudyRepository studyRepository = mock(StudyRepository.class);
```

#### `@Mock` 애노테이션으로 만드는 방법
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

### Mock이 어떻게 동작해야 하는지 관리하는 방법

### Mock의 행동을 검증하는 방법


### `@InjectMocks`
`@Autowired` 대신 가짜 객체를 넣어줄 때 사용
```java
public class MemberTest {
  @Mock
  private MemberRepository memberRepository;

  @InjectMocks
  private MemberService memberService;

  @Test
  void testSomething() {
    // Mocking
    // findById에 아무 문자열이나 넣어주면 willReturn에 정의된 응답을 주도록 가짜 동작을 만들어두는 것
    given(memberRepository.findById(anyString()))
      .willReturn(Optional.of(Member.builder()
        .age(12)
        .name("John")
        .build()));

    // getMemberDetail 메서드에서 실행 중에 memberRepository.findById()가 호출되는 부분이 나오면 위의 willReturn에 정의된 응답을 받게 된다
    MemberDetailDto memberDetail = memberService.getMemberDetail("memberId");
  }
}
```

### `@MockBean`
의존하고 있는 클래스를 가짜 빈으로 등록
```java
@MockBean
private MemberService memberService;
```

## Controller 테스트
```java
public class MemberControllerTest {

  @MockBean
  private MemberService memberService;

  protected MediaType contentType = 
    new MediaType(MediaType.APPLICATION_JSON.getType(),
            MediaType.APPLICATION_JSON.getSubtype(),
            StandardCharsets.UTF_8);

  @Test
  void getAllMembers() throws Exception {
    MemberDto memberDto1 = MemberDto.builder()
      .name("John")
      .age(12)
      .build();

    MemberDto memberDto2 = MemberDto.builder()
      .name("Tom")
      .age(14)
      .build();

    given(memberService.getAllMembers())
      .willReturn(Arrays.asList(memberDto1, memberDto2));

    mockMvc.perform(get("/members").contentType(contentType)) // mockMvc가 get으로 /members를 호출하고, 그 때 content type은 JSON으로 보내고, JSON으로 받고, UTF_8 형식의 인코딩을 사용하는 
          .andExpect(status.isOK()) // OK status를 예상
          .andDo(print()) // 요청이나 응답에 대해 상세하게 출력해줌
          .andExpect(
            jsonPath("$.[0].name", is("John")) // 응답에 오는 jsonPath의 첫 번째 배열의 name 값은 "Johe"이라고 예상
          )
          .andExpect(
            jsonPath("$.[0].age", is(12)) // 응답에 오는 jsonPath의 첫 번째 배열의 name 값은 "Johe"이라고 예상
          );

  }
}
```

## Service 테스트

## 테스트 순서
내부적으로 정해진 순서가 있다. 하지만 이 순서는 JUnit 내부 구현 로직에 따라 언제든 바뀔 수 있기 때문에 이 순서에 의지해서는 안된다. 어떻게 순서를 정하는지 드러내지 않은 이유는 각 테스트가 제대로 작성된 유닛 테스트라면 다른 유닛 테스트와는 독립적으로 실행이 가능해야 된다(서로 의존성이 없어야 된다). 

하지만 경우에 따라 특정 순서대로 테스트를 실행하고 싶을 때도 있다(ex 시나리오 테스트). 이 경우에는 `@TestInstance(Lifecycle.PER_CLASS)`와 함께 `@TestMethodOrder`를 사용할 수 있다. `@TestMethodOrder`에는 `MethodOrderer`의 구현체를 넘겨준다. 기본적으로 3개의 구현체를 제공한다.
- Alphanumeric
- OrderAnnoation
- Random



---

TODO
- `@SpringBootTest`
  - 애플리케이션을 실행하는 것과 유사하게 테스트를 할 때 모든 빈을 다 등록
  - 통합테스트라고 얘기를 한다
- given, when, then
  - given
    - mocking이나 테스트에 활용될 지역변수
  - when
    - 테스트하고자 하는 동작, 그 결과를 받아오는 것
  - then
    - 예상한대로 동작하는지 검증


<br/>

---

<br/>

출처 및 참고
- [더 자바, 애플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/the-java-application-test/dashboard)