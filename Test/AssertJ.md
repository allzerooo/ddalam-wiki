- [AssertJ](#assertj)
  - [JUnit vs AssertJ](#junit-vs-assertj)

# AssertJ
테스트를 위한 오픈 소스 라이브러리

- JUnit만 사용했을 떄 보다 더 효율적이고, 더 읽기 쉬운 코드를 작성할 수 있다

## JUnit vs AssertJ
```java
// JUnit
Assert.assertEquals(object1,object2);

// AssertJ
assertThat(object1).isEqualTo(object2);
```
- 가장 큰 차이는 비교하려는 값을 먼저 채운 다음 Assertion을 배치하는 것이다
- `assertThat`을 사용하여 비교하려는 값을 채우면 IDE에서 이후 수행할 수 있는 Assertion을 자동 완성으로 제공한다

```java
// JUnit
ArrayList<TolkienCharacter> characters = new ArrayList<>();
TolkienCharacter frodo = new TolkienCharacter("Frodo", 32);
TolkienCharacter aragorn = new TolkienCharacter("Aragorn", 62);
TolkienCharacter sam = new TolkienCharacter("Sam", 36);
characters.add(frodo);
characters.add(aragorn);
characters.add(sam);

List<TolkienCharacter> expected = new ArrayList<>();
expected.add(frodo);
expected.add(aragorn);
List<TolkienCharacter> filteredList = characters.stream()
.filter((character) -> character.name.contains("o"))
.collect(Collectors.toList());

Assert.assertEquals(expected,filteredList);


// AssertJ
ArrayList<TolkienCharacter> characters = new ArrayList<>();
TolkienCharacter frodo = new TolkienCharacter("Frodo", 32);
TolkienCharacter aragorn = new TolkienCharacter("Aragorn", 62);
TolkienCharacter sam = new TolkienCharacter("Sam", 36);

characters.add(frodo);
characters.add(aragorn);
characters.add(sam);

assertThat(characters).filteredOn(character -> character.name.contains("o")).containsOnly(aragorn, frodo);
```

```java
// JUnit
@Test(expected = IOException.class)
public void expectIOException() throws IOException {
  throw new IOException();
}

// AssertJ
assertThatThrownBy(() -> { throw new IOException("Throwing a new Exception");})
        .isInstanceOf(IOException.class).hasMessage("Throwing a new Exception");

```

<br/>

### 배열 반환 값 테스트
1. `containsExactly()` 사용
    ```java
    @Test
    void 배열_반환_값_테스트() {
        String[] actual = "a,b".split(",");
        assertThat(actual).containsExactly("a", "b");
    }
    ```
2. `contains()` 사용
   ```java
   @Test
    void 배열_반환_값_테스트() {
        String[] actual = "a,b".split(",");
        assertThat(actual).contains("a");
    }
   ```
   
<br/>

---

<br/>

출처 및 참고
- [What’s the difference between JUnit and AssertJ](https://annaduldiier.medium.com/assertj-vs-junit-483b7d6dc997)