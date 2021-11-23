- [```Optional```](#optional)
  - [값이 없는 상황](#값이-없는-상황)
    - [보수적인 자세로 NullPointException 줄이기](#보수적인-자세로-nullpointexception-줄이기)
    - [null 때문에 발생하는 문제](#null-때문에-발생하는-문제)
  - [Optional 클래스](#optional-클래스)
    - [null 참조와 Optional.empty()의 차이](#null-참조와-optionalempty의-차이)
    - [Optional 클래스의 역할](#optional-클래스의-역할)
  - [Optional 적용 패턴](#optional-적용-패턴)
    - [Optional 객체 만들기](#optional-객체-만들기)
      - [빈 Optional](#빈-optional)
      - [null이 아닌 값을 포함하는 Optional 만들기](#null이-아닌-값을-포함하는-optional-만들기)
      - [null 값을 저장할 수 있는 Optional 만들기](#null-값을-저장할-수-있는-optional-만들기)
    - [맵으로 Optional의 값을 추출하고 변환하기](#맵으로-optional의-값을-추출하고-변환하기)
  - [Optional 객체 생성하기 - ```of()``` 또는 ```ofNullable()``` 사용](#optional-객체-생성하기---of-또는-ofnullable-사용)
  - [Optional 초기화 - ```empty()```](#optional-초기화---empty)
  - [Optional 객체의 값 가져오기 - ```get()``` 또는 ```orElse()``` 또는 ```orElseGet()``` 또는 ```orElseThrow()``` 사용](#optional-객체의-값-가져오기---get-또는-orelse-또는-orelseget-또는-orelsethrow-사용)
  

# ```Optional```

## 값이 없는 상황
### 보수적인 자세로 NullPointException 줄이기
필요한 곳에 null 확인 코드를 추가해서 null 문제를 해결
- 어떤 객체가 null을 가지지 않는다는걸 단정하기 어렵다
- 따라서 모든 변수가 null인지 의심하는 if문의 증가로 코드의 들여쓰기 수준이 증가한다
- 이를 반복하다보면 코드의 구조가 엉망이 되고 가독성도 떨어진다

따라서 null로 값이 없다는 사실을 표현하는 것은 좋은 방법이 아니며, 값이 있거나 없음을 표현할 수 있는 방법이 필요하다.

### null 때문에 발생하는 문제
- NullPointException은 자바에서 가장 흔히 발생하는 에러다
- 중첩된 null 확인 코드를 추가해야 하므로 null 때문에 코드의 가독성이 떨어진다
- 모든 참조 형식에 null을 할당할 수 있는데 이런 식으로 null이 할당되기 시작하면 애초에 null이 어떤 의미로 사용되었는지 알 수 없다
- null이 할당되었을 때 올바른 값인지, 잘못된 값인지 판단할 정보가 없다

## Optional 클래스
- 선택형 값을 캡슐화하는 클래스다. 값이 있을수도 있고, 없을수도 있다면 null을 할당한느 것이 아니라 변수를 Optional로 설정하는 것이다
- 값이 있으면 Optional 클래스는 값을 감싸고, 값이 없으면 Optional.empty 메서드로 Optional을 반환한다

### null 참조와 Optional.empty()의 차이
- null 대신 Optional을 사용하면 값이 없을 수 있음을 명시적으로 보여준다
- 따라서 Optional이 아닌 변수는 반드시 값을 가져야 함을 보여준다
- 반드시 값을 가져야하는 변수에 값이 없다면 예외를 처리하는 코드를 추가하는 것이 아니라 값이 없는 이유가 무엇인지 밝혀서 문제를 해결할 수 있다
- Optional을 사용하면 값이 없는 상황이 데이터에 문제가 있는 것인지, 알고리즘의 버그인지 명확히 구분할 수 있다

### Optional 클래스의 역할
모든 null 참조를 Optional로 대치하는 것은 바람직하지 않다. Optional의 역할은 더 이해하기 쉬운 API를 설계하도록 돕는 것이다. 즉, 메서드의 시그니처만 보고도 선택형 값인지 여부를 구별할 수 있다. Optional을 사용하면 이를 언랩해서 값이 없을 수 있는 상황에 적절하게 대응하도록 강제하는 효과가 있다

## Optional 적용 패턴

### Optional 객체 만들기

#### 빈 Optional
Optional.empty()
```java
Optional<Car> optCar = Optional.empty();
```

#### null이 아닌 값을 포함하는 Optional 만들기
Optional.of()
```java
Optional<Car> optCar = Optional.of(car);
```
car가 null이면 즉시 NullPointException이 발생한다. Optional을 사용하지 않았다면 car에 접근할 때 에러가 발생했을 것이다

#### null 값을 저장할 수 있는 Optional 만들기
Optional.ofNullable()
```java
Optional<Car> optCar = Optional.ofNullable(car);
```
car가 null이면 빈 Optional 객체가 반환된다

### 맵으로 Optional의 값을 추출하고 변환하기



Optional<T>는 지네릭 클래스로 T타입의 객체를 감싸는 래퍼 클래스이다. 값을 Optional 객체에 담아서 사용하면 매번 if문으로 값이 null인지 확인하는 대신 Optional에 정의된 메서드를 통해 간단히 처리할 수 있다.

## Optional 객체 생성하기 - ```of()``` 또는 ```ofNullable()``` 사용

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

## Optional 객체의 값 가져오기 - ```get()``` 또는 ```orElse()``` 또는 ```orElseGet()``` 또는 ```orElseThrow()``` 사용

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
- 모던 자바 인 액션(라울-게이브리얼 우르마, 마리오 푸스코, 앨런 마이크로프트)

