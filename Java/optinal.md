# ```Optinal```

## Optinal 객체 생성하기 - ```of()``` 또는 ```ofNullable()``` 사용

```java
Optional<String> optionalValue1 = Optional.of(value);
Optional<String> optionalValue2 = Optional.ofNullable(value);
```

- null 값도 허용하는 옵셔널을 만들려면 ofNullable()을 사용해야 한다. of()는 매개변수 값이 null이면 NullPointerException을 던진다

<br/>

## Optional 초기화

```java
Optinal<T> optionalValue = Optinal.empty();
```
- null로 초기화하는 것도 가능하다

<br/>

---

출처 

- Java의 정석 - 남궁 성
- Effective Java - 조슈아 블로크

