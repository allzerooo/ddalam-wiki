- [Lombok](#lombok)
  - [기능](#기능)
  - [각종 예상치 못한 동작](#각종-예상치-못한-동작)
  - [대체](#대체)

# Lombok

- "Never write another getter or equals method again"
- Boilerplate code를 줄여주는 도구
  - Boilerplate code : 늘 똑같이 반복되는 코드
- 생산성 향상에 기여

## 기능
- `@Data`
  - `@Getter` + `@Setter` + `@RequiredArgsConstructor` + `@ToString` + `@EqualsAndHashCode`
  - 많은걸 지원해줘서 편한만큼 조심히 사용해야된다
- `@RequiredArgsConstructor`
  - 필수 필드만(`final`) 인자로 받는 생성자를 만들어준다
- `@Value`
  - 불변 객체를 만들때 사용
  - `@Getter` + `@FieldDefaults(makeFinal=true, level=AccessLevel.PRIVATE)` + `@AllArgsConstructor` + `@ToString` + `@EqualsAndHashCode`
- `@Builder`
  - 모든 필드에 method chain 기법으로 접근이 가능하고, 접근하지 않아도 된다

## 각종 예상치 못한 동작
- 사용자의 의도와 컴파일러의 눈을 피해가는 동작
  - ex 1)
      ```java
      @Data
      @Builder
      public class Student {
          private final String name;
          private Integer age;
      }
      ```
      ```java
      @Test
      void lombokTest() {
          Student martie = Student.builder()
                  .age(30)
                  .build();
      }
      ```
      `name`을 null로 채우고 싶을 수 있지만 `final`은 필수 값을 의도했을 수 있는데, Builder 패턴을 사용했을 때 `name`이 빠진게 실수냐? 의도냐?
  - ex 2)
      ```java
      @Value(staticConstructor = "of")
      public class Student {
        String name;
        Integer age;
        Integer height;
      }
      ```
      `age`와 `height`의 순서를 바꾸면
      ```java
      @Value(staticConstructor = "of")
      public class Student {
        String name;
        Integer height;
        Integer age;
      }
      ```
      `age`와 `height`가 같은 Integer 타입이라 컴파일 에러가 나지 않은 채로 테스트를 통과하게 된다
      ```java
      @Test
      void lombokTest() {
        Student martie = Student.of("Martie", 30, 176);
        Student fred = Student.of("Fred", 21, 171);

        assertThat(fred).isNotEqualsTo(martie);
      }
      ```
      애노테이션 방식으로 동작하기 때문에 개발자가 인지하지 못한 문제가 발생
- `@ToString` 순환 참조 문제
  ```java
  @Data(staticConstructor = "of")
  @ToString
  public class Car {
    private final String name;
    private List<Seat> seats;
  }
  ```
  ```java
  @AllArgsConstuctor(staticName = "of")
  @ToString
  public class Seat {
    private final Car car;
  }
  ```
  ```java
  @Test
  void test() {
    Car car = Car.of("My Car");
    Seat seat = Seat.of(car);
    car.setSeats(List.of(seat));

    System.out.println(car);
  }
  ```
  - Car → Seat → Car → ... 반복하게 되어 `StackOverflowError`가 발생한다
  - JPA Entity의 연관관계 매핑에서 많이 발생한다

  해결 방법 (`@ToString`에 `exclude` 값 지정)
  ```java
  @AllArgsConstuctor(staticName = "of")
  @ToString(exclude = "car")
  public class Seat {
    private final Car car;
  }
  ```
  ```bash
  Car(name=My Car, seats=[Seat()])
  ```
  단점
  - `exclude` 값을 문자열로 지정한 것. 필드명을 바꾸면 같이 바뀌지 않는다
  - 따라서, 필드에 바로 exclude를 지정할 수 있는 방법이 생겼다
    ```java
    @AllArgsConstuctor(staticName = "of")
    @ToString
    public class Seat {
      @ToString.Exclude private final Car car;
    }
    ```
- 과도한 애노테이션, 관례 기반 코드 스타일: 동작 예측이 어렵다는 지적
- 명시적이고 테스트가 쉬운 코드로 회귀하려는 움직임

## 대체
- Kotlin: data class 등장
- Java 14 : record 등장
```java
public record Student(
  String name,
  Integer age,
  Grade grade
) {
  public static Student of(String name, Integer age, Grade grade) {
    return new Student(name, age, grade);
  }

  public enum Grade {
    A, B, C
  }
}
```
```java
@Test
void java14RecordTest() {
  Student martie = Student.of("Martie", 30, Student.Grade.A);
  Student fred = Student.of("Fred", 21, Student.Grade.A);

  assertThat(fred).isNotEqualsTo(martie);

  System.out.println("I am a " + fred);
  System.out.println(fred.name());
}
```
- `public record Student` 위에 적어야하는 외부 의존성이 하나도 없는 깔끔한 상태로 불변 객체를 생성할 수 있게 되었다
- `fred.name()`과 같이 getter 네이밍 컨벤션의 변화
- 이미 equals, toString이 구현되어 있음
  ```bash
  I am a Student[name=Fred, age=30, grade=A]
  ```


<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)
- [Project Lombok](https://projectlombok.org/)