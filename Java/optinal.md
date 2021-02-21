# ```Optinal```

## Optinal 객체 생성하기 - ```of()``` 또는 ```ofNullable()``` 사용

```java
Optional<String> optionalValue1 = Optional.of(value);
Optional<String> optionalValue2 = Optional.ofNullable(value);
```

- ```null``` 값도 허용하는 옵셔널을 만들려면 ```ofNullable()```을 사용해야 한다. ```of()```는 매개변수 값이 ```null```이면 ```NullPointerException```을 던진다

<br/>

## Optional 초기화 - ```empty()```

```java
Optinal<T> optionalValue = Optinal.empty();
```
- null로 초기화하는 것도 가능하다

<br/>

## Optinal 객체의 값 가져오기 - ```get()``` 또는 ```orElse()``` 또는 ```orElseGet()``` 또는 ```orElseThrow()``` 사용

```java
Optinal<String> optinalValue = Optinal.of("abc");
String str1 = optinalValue.get();
String str1 = optinalValue.orElse("");  // null일 때 ""를 반환
```

- ```get()```은 optionalValue에 저장된 값이 null일 때 ```NoSuchElementException```이 발생하며, ```orElse()```를 사용하면 null일 때 대체할 값을 지정할 수 있다
- ```orElse()```의 변형으로 null을 대체할 값을 람다식으로 지정할 수 있는 ```orElseGet()```, 지정된 예외를 발생시키는 ```orElseThrow()```가 있다

<br/>

- ```isPresent()```는 Optional 객체의 값이 null이면 false를, 아니면 true를 반환한다
- ```ifPresent(Consumer<T> block)```은 Optinal 객체의 값이 null이면 아무일도 하지 않고, 아니면 주어진 람다식을 실행한다

```java
if(Optional.ofNullable(str).isPresent()) {
    System.out.println(str);
}
```

```java
Optional.ofNullable(str).ifPresent(System.out::println);
```

<br/>

---

출처 

- Java의 정석 - 남궁 성
- Effective Java - 조슈아 블로크

