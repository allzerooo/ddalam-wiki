### 프로그램 오류

<br/>

소스코드( *.java ) → 컴파일러 ( 오타, 잘못된 구문, 자료형 체크 등 ) → 컴파일 성공 → 클래스 파일( *.class ) → 클래스 파일을 실행

<br/>

발생시점에 따라,

- 컴파일 에러 : 컴파일 시 발생하는 에러
- 런타임 에러 : 프로그램 실행도중 발생하는 에러
    - 에러(error) : 프로그램 코드에 의해 수습될 수 없는 심각한 오류
    - 예외(exception) : 프로그램 코드에 의해 수습될 수 있는 미약한 오류
        - 프로그래머가 적절한 코드를 작성해 프로그램의 비정상적인 종료를 막을 수 있다
- 논리적 에러 : 컴파일도 잘되고, 실행도 잘되지만 의도와 다르게 동작하는 것

<br/>

### 예외 클래스의 계층구조

자바는 실행 시 발생할 수 있는 모든 `Error`와 `Exception`을 클래스로 정의하였다

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/43d696ef-0dfc-478c-baa4-874368410979/java-exception_1.png](https://user-images.githubusercontent.com/7088950/80301746-de496f00-87e0-11ea-976c-ed8d7785d7c6.png)

[출처] [https://kookyungmin.github.io/language/2018/06/06/java_18/](https://kookyungmin.github.io/language/2018/06/06/java_18/)

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fa7258c4-0579-42f7-b649-079128d0d382/java-exception_2.png](https://user-images.githubusercontent.com/7088950/80301758-f91be380-87e0-11ea-89a1-2a8994149df9.png)

[출처] [https://kookyungmin.github.io/language/2018/06/06/java_18/](https://kookyungmin.github.io/language/2018/06/06/java_18/)

<br/>

`Exception` 클래스들은 두 그룹으로 나눌 수 있다

- `RunTimeException`클래스와 그 자손들 : 프로그래머의  실수로 발생하는 예외
- `Exception`클래스와 `RunTimeException`클래스와 그 자손들을 제외한 클래스들 : 외적인 요인에 의해 발생하는 예외( 사용자들의 동작에 의해서 발생하는 경우가 많다 )

<br/>

### 예외 처리하기

> 프로그램의 실행도중에 발생하는 에러는 어쩔 수 없지만, 예외는 프로그래머가 이에 대한 처리를 미리 해주어야 한다.

- 예외가 발생하면, 발생한 예외에 해당하는 클래스의 인스턴스가 만들어진다
- 처리되지 못한 예외는 JVM의 예외처리기(`UncaughtExceptionHandler`)가 받아서 예외의 원인을 화면에 출력한다
- 예외의 종류와 일치하는 단 한 개의 `catch` 블럭만 수행된다

<br/>

예외 정보를 출력

- `printStacktTrace()` : 예외발생 당시의 호출스택(Call Stack)에 있었던 메서드의 정보와 예외 메시지를 화면에 출력
- `getMessage()` : 발생한 예외클래스의 인스턴스에 저장된 메시지를 얻을 수 있다

```
// ExceptionEx8.java 파일의 7번째 줄에서 0으로 나누는 연산을 했을 때
// printStackTrace()가 출력하는 정보
java.lang.ArithmeticException: / by zero
      at ExceptionEx8.main(ExceptionEx8.java:7)

// getMessage()가 출력하는 정보
/ by zero
```