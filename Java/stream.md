- [Stream](#stream)
   - [Stream이 제공하는 유용한 기능](#stream이-제공하는-유용한-기능)

# Stream

## Stream이 제공하는 유용한 기능

저칼로리 요리명을 반환하고, 칼로리를 기준으로 요리를 정렬하는 Java 7 코드
```java
public static List<String> getLowCaloricDishesNamesInJava7(List<Dish> dishes) {
   List<Dish> lowCaloricDishes = new ArrayList<>();

   // 칼로리가 400보다 작은 요리를 list에 추가
   for (Dish d : dishes) {
      if (d.getCalories() < 400) {
         lowCaloricDishes.add(d);
      }
   }

   // 선택된 요리를 칼로리를 기준으로 정렬
   Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
      @Override
      public int compare(Dish d1, Dish d2) {
         return Integer.compare(d1.getCalories(), d2.getCalories());
      }
   });

   // 정렬된 리스트에서 요리명을 얻기
   List<String> lowCaloricDishesName = new ArrayList<>();
   for (Dish d : lowCaloricDishes) {
      lowCaloricDishesName.add(d.getName());
   }

   return lowCaloricDishesName;
}
```
`lowCaloricDishes`는 컨테이너 역할만 하는 '가비지 변수'이다. Java 8에서는 이러한 세부 구현은 라이브러리 내에서 모두 처리한다.

<br/>

Java 8 코드
```java
public static List<String> getLowCaloricDishesNamesInJava8(List<Dish> dishes) {
   return dishes.stream()
      .filter(d -> d.getCalories() < 400)
      .sorted(comparing(Dish::getCalories))
      .map(Dish::getName)
      .collect(toList());
}
```
`stream()`을 `parallelStream()`으로 바꾸면 멀티코어 아키텍처에서 병렬로 실행할 수 있다

<br/>

1. 선언형 : 더 간결하고 가독성이 좋아진다
   - 선언형으로 코드를 구현할 수 있다. 즉, 루프와 if 조건문 등의 제어 블록을 사용해서 어떻게 동작을 구현할지 지정할 필요 없이 '저칼로리 요리만 선택하라' 같은 동작의 수행을 지정할 수 있다.
2. 조립할 수 있음 : 유연성이 좋아진다
   - filter, sorted, map, collect 같은 여러 빌딩 블록 연산을 연결해서 복잡한 데이터 처리 파이프라인을 만들 수 있다
3. 병렬성 : 성능이 좋아진다
   - 데이터 처리 과정을 병렬화면서 스레드와 락을 걱정할 필요가 없다

## 정의
많은 수의 데이터를 다룰 때, 데이터 소스(베열, 컬렉션, 파일 등)가 무엇이던 같은 방식으로 다룰 수 있도록 한 것

<br/>
<br/>

## 스트림이 나오기 이전 방법의 문제점

컬렉션이나 배열과 같이 많은 수의 데이터를 다룰 때

- for문과 Iterator를 이용해 작성된 코드는 길고, 알아보기 어려우며, 재사용성이 떨어진다
- 데이터 소스마다 다른 방식으로 다뤄야한다. 예를 들어, List를 정렬할 때는 `Collections.sort()`를 사용하며, 배열을 정렬할 때는 `Arrays.sort()`를 사용한다.

<br/>
<br/>

## 장점
- 데이터 소스가 무엇이던 같은 방식으로 다룰 수 있다
- 코드의 재사용성이 높아진다
- 제공하는 다양한 연산을 이용해 복잡한 작업들을 간단히 처리할 수 있다

<br/>
<br/>

## 특징
- 데이터 소스로 부터 데이터를 읽기만 할 뿐 데이터 소스를 변경하지 않는다
- 일회용이다
- 작업을 내부 반복으로 처리한다

<br/>
<br/>

## 스트림 만들기

### 컬렉션으로 스트림 만들기

컬렉션의 최고 조상인 `Collection`에 `stream()`이 정의되어 있다. 따라서 `Collection`의 자손을 구현한 클래스들은 모두 이 메서드로 스트림을 생성할 수 있다.

<br/>
<br/>

## 연산

### 정의
스트림에 정의된 메서드 중 데이터 소스를 다루는 작업을 수행하는 것

<br/>

### 종류
1. 중간 연산
   1. 정의
      1. 연산 결과가 스트림인 연산
   2. 특징
      1. 스트림에 중간 연산을 연속해서 연결할 수 있다
   3. 종류
      1. `Stream<T> distinct()`
      2. `filter()`: 조건에 안 맞는 요소 제외
      3. ...
2. 최종 연산
   1. 정의
      1. 연산 결과가 스트림이 아닌 연산
   2. 특징
      1. 스트림의 요소를 소모하며 단 한번만 연산이 가능하다
   3. 종류
      1. `long count()`
      2. `reduce()` : 스트림의 요소를 하나씩 줄여가면서 계산한다
      3. ...
- 최종 연산이 수행되기 전까지 중간 연산이 수행되지 않는다(지연된 연산)

## 연산 - 중간 연산

### 필터링
```java
List<Dish> vegetarianMenu = menu.stream()
        .filter(Dish::isVegetarian)
        .collect(toList());
```

### 고유 요소 필터링
고유 여부는 스트림에서 만든 객체의 hashCode, equals로 결정된다
```java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
numbers.stream()
        .filter(i -> i % 2 == 0)
        .distinct()
        .forEach(System.out::println);
```

### 프레디케이트를 이용한 슬라이싱
```java
List<String> sliceMenu = menu.stream()
				.takeWhile(dish -> dish.getCalories() < 320)
				.map(Dish::getName)
				.collect(toList());
```
- 320 칼로리보다 크거나 같은 요리가 나왔을 때 반복 작업을 중단
- 작은 리스트에서는 별거 아닌 것처러 보일 수 있지만 아주 많은 요소를 포함하는 스트림에서는 상당한 차이가 될 수 있다

```java
List<String> sliceMenu = menu.stream()
				.dropWhile(dish -> dish.getCalories() < 320)
				.map(Dish::getName)
				.collect(toList());
```
- 320 칼로리 이상의 요리를 선택
- 프레디케이트가 처음으로 거짓이 되는 지점에서 작업을 중단하고 남은 모든 요소를 반환
- 무한한 남은 요소를 가진 무한 스트림에서도 동작한다

### 스트림 축소
```java
List<String> collect = menu.stream()
				.filter(dish -> dish.getCalories() > 300)
				.limit(3)
				.map(Dish::getName)
				.collect(toList());
```
- 프레디케이트와 일치하는 처음 세 요소를 선택한 다음 즉시 결과를 반환
- 소스가 정렬되어 있지 않았다면 limit의 결과도 정렬되지 않은 상태로 반환된다

### 요소 건너뛰기
```java
List<String> collect = menu.stream()
				.filter(dish -> dish.getCalories() > 300)
				.skip(2)
				.map(Dish::getName)
				.collect(toList());
```
- 처음 n개 요소를 제외한 스트림을 반환
- n개 이하의 요소를 포함하는 스트림에 skip(n)을 호출하면 빈 스트림이 반환

### 스트림의 각 요소에 함수 적용하기
```java
List<String> words = Arrays.asList("Modern", "Java", "In", "Action");
List<Integer> wordLengths = words.stream()
        .map(String::length)
        .collect(toList());
```
- map()의 인수로 제공된 함수는 각 요소에 적용되며 함수를 적용한 결과가 새로운 버전의 스트림을 만든다(매핑)

### 스트림 평면화
```java
List<String> words = Arrays.asList("Hello", "World");
List<String> collect = words.stream()
        .map(word -> word.split(""))
        .flatMap(Arrays::stream)
        .distinct()
        .collect(toList());
```
- flatMap은 하나의 평면화된 스트림을 반환한다

## 연산 - 최종 연산

### findFirst vs findAny
병렬 실행에서는 첫 번째 요소를 찾기 어렵다. 따라서 요소의 반환 순서가 상관없다면 병렬 스트림에서는 제약이 적은 findAny를 사용한다

### 리듀싱
- 모든 스트림 요소를 처리해서 값으로 도출하는 질의
- `reduce` 메서드의 장점과 병렬화

## 기본형 데이터 소스 스트림

Stream<T>의 오토박싱, 언박싱으로 인한 비요율을 줄이기 위해 기본형으로 데이터 소스의 요소를 다루는 스트림이 제공된다

<br/>

### 종류
1. `IntStream`
2. `LongStream`
3. `DoubleStream`

<br/>

--- 

<br/>

출처 및 참고
- Java의 정석 (남궁 성)
- 모던 자바 인 액션 (라울-게이브리얼 우르마, 마리오 푸스코, 앨런 마이크로프트)
