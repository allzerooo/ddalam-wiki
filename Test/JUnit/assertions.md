# Assertions

## Object Assertions

### 1. `isEqualTo()` : 객체의 참조를 비교
```java
@Test
    void compareObjects() {
        Dog fido = new Dog("Fido", 5.25F);
        Dog fidosClone = new Dog("Fido", 5.25F);

        // isEqualTo()는 객체의 참조를 비교하기 때문에 실패
        assertThat(fido).isEqualTo(fidosClone);
```

<br/>

### 2. `isEqualToComparingFieldByFieldRecursively()` : 객체의 필드를 재귀적으로 비교
```java
@Test
    void compareObjects() {
        Dog fido = new Dog("Fido", 5.25F);
        Dog fidosClone = new Dog("Fido", 5.25F);

        // 객체의 필드를 재귀적으로 비교하기 때문에 성공
        assertThat(fido).isEqualToComparingFieldByFieldRecursively(fidosClone);
    }
```

<br/>

[AbstractObjectAssert](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractObjectAssert.html)

<br/>

## Boolean Assertions

### 1. `isTrue()`, `isFalse()`
```java
@Test
void assertBoolean() {
    // Boolean에 대한 테스트는 isTrue(), isFalse()를 사용할 수 있다
    assertThat("".isEmpty()).isTrue();
}
```

<br/>

## Iterable/Array Assertions
```java
@Test
void assertIterableOrArray() {
    // 요소가 포함되어 있는지
    List<String> list = Arrays.asList("1", "2", "3");
    assertThat(list).contains("1");
    // 여러개의 element가 포함되어 있는지도 가능
    assertThat(list).contains("1", "3");

    // 비어있는지
    assertThat(list).isNotEmpty();

    // 주어진 문자로 시작되는지
    assertThat(list).startsWith("1");

    // 하나의 객체에 둘 이상의 assertion을 연결할 수 있다
    assertThat(list)
            .isNotEmpty()
            .contains("1")
            .doesNotContainNull()
            .containsSequence("2", "3"); // 실제 그룹이 시퀀스 값 사이에 추가 값없이 순서대로 지정된 시퀀스를 포함하는지 확인
}
```
<br/>

[AbstractIterableAssert](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractIterableAssert.html)

<br/>

---

<br/>
출처
- [Introduction to AssertJ](https://www.baeldung.com/introduction-to-assertj)