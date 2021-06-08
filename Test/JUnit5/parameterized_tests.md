# Parameterized Tests
다른 매개변수로 단일 테스트를 여러번 실행할 수 있다

<br/>

## Gradle 의존성 추가
```java
testCompile("org.junit.jupiter:junit-jupiter-params:5.7.0")
```

<br/>

## 방법

매개변수를 사용하는 테스트는 `@ParameterizedTest` annotation을 추가해야 한다.

그리고 `@ValueSource`를 사용하면 메서드에 할당할 매개변수 값의 배열을 지정할 수 있다.

```java
@ParameterizedTest
@ValueSource(ints = {1, 3, 5, -3, 15, Integer.MAX_VALUE}) // six numbers
void isOdd_ShouldReturnTrueForOddNumbers(int number) {
    assertTrue(Numbers.isOdd(number));
}
```

위 테스트는 `isOdd()` 메서드가 `@ValueSorce`의 각 값으로 총 6번 실행된다.

<br/>

## Argument Sources

### Simple Values

`@ValueSource`에 사용 가능한 타입
- short
- byte
- int
- long
- float
- double
- char
- java.lang.String
- java.lang.Class

<br/>

한 가지 제한된 점은,

`@ValueSource`를 통해 `null`을 전달할 수 없다. 타입이 `String`, `Class`의 경우에도 `null`을 전달할 수 없다.

<br/>

### Null and Empty Values

**1. `@NullSource`**

JUnit 5.4부터 `@NullSource`를 사용해 테스트 메서드에 매개변수로 단일 `null`값을 전달할 수 있다

```java
@ParameterizedTest
@NullSource
void isBlank_ShouldReturnTrueForNullInputs(String input) {
    assertTrue(Strings.isBlank(input));
}
```

<br/>

**2. `@EmptySource`**

`primitive` 데이터 타입은 `null` 값이 허용되지 않기 때문에 `@NullSource`를 사용할 수 없고, `@EmptySource`를 사용해 빈 값을 전달할 수 있다. 또한, `String`, 컬렉션, 배열에 대해서도 빈 값을 전달할 수 있다.

```java
@ParameterizedTest
@EmptySource
void isBlank_ShouldReturnTrueForEmptyStrings(String input) {
    assertTrue(Strings.isBlank(input));
}
```

<br/>

**3. `@NullAndEmptySource`**

`null`과 빈 값을 모두 전달하기 위해 `@NullAndEmptySource`를 사용할 수 있다.

<br/>

`@ValueSource`와 위 3가지 annotation을 함께 사용할수도 있다.

```java
@ParameterizedTest
@NullAndEmptySource
@ValueSource(strings = {"  ", "\t", "\n"})
void isBlank_ShouldReturnTrueForAllTypesOfBlankStrings(String input) {
    assertTrue(Strings.isBlank(input));
}
```
```text
# input
null
빈 값
빈 값 
tab(빈 값)
줄바꿈(빈 값)
```

<br/>

### CSV Literals

`@CsvSource`를 사용하면, 여러 파라미터를 전달할 수 있다.
쉼표로 구분된 배열 값으로 구성되며, 매번 하나의 배열 항목을 가져와 쉼표로 분할하고, 각 매배변수로 전달한다.

```java
@ParameterizedTest
@CsvSource({"test,TEST", "tEst,TEST", "Java,JAVA"})
void toUpperCase_ShouldGenerateTheExpectedUppercaseValue(String input, String expected) {
    String actualValue = input.toUpperCase();
    assertEquals(expected, actualValue);
}
```

기본적으로 쉼표를 구분 기호로 사용하지만 `delimiter` 속성을 사용해 구분 기호를 정의할 수 있다.
```java
@CsvSource(value = {"test:test", "tEst:test", "Java:java"}, delimiter = ':')
```


<br/>

---

<br/>

출처

- [Guide to JUnit 5 Parameterized Tests](https://www.baeldung.com/parameterized-tests-junit-5)