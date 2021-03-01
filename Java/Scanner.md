# Scanner

Scanner 클래스 객체 생성

```java
Scanner sacnner = new Scanner(System.in);
```

`nextLine()`

```java
String input = scanner.nextLine();
```

- 입력 대기 상태에 있다가 입력을 마치고 Enter를 누르면 입력한 내용이 문자열로 반환된다.
- 입력되는 값을 공백으로 구분되는 토큰 단위(`\t`, `\f`, `''`, `\n`)로 읽는다.

<br/>

`nextInt()`, `nextFloat()`과 같이 숫자로 바로 입력을 받을 수 있는 메서드도 있다.

<br/>

`System.in`

- 키보드와 연결된 Java의 표준 입력 스트림이다.

<br/>

---

출처

- Java의 정석 (남궁 성)
- [[Java] 자바_스캐너(Scanner)](https://mine-it-record.tistory.com/103)
