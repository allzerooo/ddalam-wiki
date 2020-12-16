# 조건에 따라 테스트 실행하기

OS, 자바 버전, 환경변수와 같은 특정 값에 따라 테스트를 실행하거나, 실행하지 않아야 할 때

<br/>

### `assumeTrue()`

- 조건을 만족할때만 다음 테스트를 실행
- 테스트가 안된 것처럼 표시됨

<br/>

### `assumingThat()`

<br/>

### 애노테이션을 사용한 방법

- `@EnabledOnOs(OS.MAC)`
- `@DisabledOnOs(OS.MAC)`
- `@EnabledOnJre(JRE.JAVA_8, JRE.JAVA_9)`
- `@EnabledIfEnvironmentVariable(name="TEST_ENV", matches = "local")`
- `@EnabledIfSystemProperty`, `@DisabledIfSystemProperty`
- `@EnabledIf`, `@DisabledIf`

<br/>

---

<br/>

[출처]

- 인프런 더 자바 코드를 테스트하는 다양한 방법 (백기선)
