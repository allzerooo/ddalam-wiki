# 정규식 (Regular Expression)

## Patter, Matcher

```java
Pattern p = Pattern.compile("c[a-z]*");
```

- Pattern : 정규식을 정의하는데 사용
- `compile()` 메서드는 정규식 문자열을 인자로 받아 Pattern 인스턴스를 생성한다
- 이렇게 생성된 Pattern 인스턴스는 Matcher 를 만드는데 사용된다

```java
Matcher m = p.matcher("ca");
```

- Matcher : 정규식을 데이터와 비교하는 역할
- 정규식으로 비교할 대상을 인자로 Pattern의 `matcher()`를 호출해 Matcher 인스턴스를 생성한다

```java
if (m.matches())
```

- Matcher 인스턴스의 `matches()`를 호출해서 정규식에 부합하는지 확인한다

<br/>

### Matcher 클래스의 주요 메서드

- find()
  - 패턴과 일치하는 부분을 찾으면 `true`, 찾지 못하면 `false`를 반환
  - `find()`를 호출해서 패턴과 일치하는 부분을 찾아낸 다음, 다시 `find()`를 호출하면 이전에 발견한 다음 부분부터 다시 패턴 매칭을 시작한다
  - 패턴과 일치하는 부분을 찾으면 Matcher 인스턴스의 `start()`와 `end()`로 일치하는 위치를 알아낼 수 있다

<br/>
<br/>

## Grouping

정규식의 일부를 괄호로 나누어 묶어서 그룹화할 수 있고, 그룹화된 부분은 `group(int i)`를 이용해서 얻을 수 있다

---

<br/>

출처

- Java의 정석(남궁 성)
- [자바 정규표현식 Pattern, Matcher](https://ktko.tistory.com/entry/JAVA-%EC%9E%90%EB%B0%94%EC%9D%98-%EC%A0%95%EA%B7%9C%ED%91%9C%ED%98%84%EC%8B%9D-Pattern-Matcher)
