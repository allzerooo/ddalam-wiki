- [Lombok](#lombok)
  - [기능](#기능)
  - [음..](#음)

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

## 음..
- 각종 예상치 못한 동작
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
        - `name`을 null로 채우고 싶을 수 있지만 `final`은 필수 값을 의도했을 수 있는데, Builder 패턴을 사용했을 때 `name`이 빠진게 실수냐? 의도냐?
    - ex 2)
        ```java
        ```
  
- 과도한 애노테이션, 관례 기반 코드 스타일: 동작 예측이 어렵다는 지적
- 명시적이고 테스트가 쉬운 코드로 회귀하려는 움직임


<br/>

---

<br/>

출처 및 참고
- [한 번에 끝내는 Spring 완.전.판 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_spring)
- [Project Lombok](https://projectlombok.org/)