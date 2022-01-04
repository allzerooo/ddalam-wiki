- [JUnit 5](#junit-5)
  - [`@ExtendWith()`](#extendwith)
  - [`@WebMvcTest()`](#webmvctest)
    - [`MockMvc`](#mockmvc)
  - [Mockito](#mockito)
    - [`@InjectMocks`](#injectmocks)
    - [`@MockBean`](#mockbean)
  - [Controller 테스트](#controller-테스트)
  - [Service 테스트](#service-테스트)

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
- spring-boot-stater-test에 내장되어 있음


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