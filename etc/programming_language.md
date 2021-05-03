### 키워드 매개 변수를 지원하는 언어
ex) python

<br/>

```python
reduce(source=myexpression, to="USD")
```
```python
reduce(to="USD")
```
위 코드와 같이 매개 변수의 이름을 명시화해서 의도를 분명히 드러낼 수 있다

<br/>

### 위치 매개 변수를 지원하는 언어
ex)Java

<br/>

```java
Bank.reduce(Expression, String)
```
```java
Expression.reduce(String)
```
두 메서드가 어떻게 다른지에 대해 코드에 명확히 담아내는 것이 쉽지 않다

<br/>

---

<br/>

출처
- 테스트 주도 개발 (켄트 벡)