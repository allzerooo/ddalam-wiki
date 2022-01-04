- [JUnit 5](#junit-5)
  - [Mockito](#mockito)
    - [`@ExtendWith(MockitoExtension.class)`](#extendwithmockitoextensionclass)
    - [`@InjectMocks`](#injectmocks)

# JUnit 5
Java의 Unit 테스트를 위한 프래임워크

## Mockito
- spring-boot-stater-test에 내장되어 있음

### `@ExtendWith(MockitoExtension.class)`
Mockito라는 외부 기능을 사용해 테스트하겠다고 선언

### `@InjectMocks`
`@Autowired` 대신 가짜 객체를 넣어줄 때 사용
```java
@Mock
private MemberRepository memberRepository;

@InjectMocks
private MemberService memberService;

@Test
public void testSomething() {
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
```

---

TODO
- `@SpringBootTest`
  - 애플리케이션을 실행하는 것과 유사하게 테스트를 할 때 모든 빈을 다 등록
  - 통합테스트라고 얘기를 한다