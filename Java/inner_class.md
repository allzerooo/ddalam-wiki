- [내부 클래스 (inner class)](#내부-클래스-inner-class)
		- [장점](#장점)
	- [내부 클래스의 종류](#내부-클래스의-종류)
		- [인스턴스 내부 클래스](#인스턴스-내부-클래스)
		- [정적 내부 클래스](#정적-내부-클래스)
		- [지역 내부 클래스](#지역-내부-클래스)
		- [익명 내부 클래스](#익명-내부-클래스)

# 내부 클래스 (inner class)
- 클래스 내부에 선언한 클래스 (중첩 클래스라고도 함)
- **외부 클래스와 밀접한 연관이 있는 경우, 외부 클래스 외 다른 클래스에서 사용할 일이 거의 없는 경우에 내부 클래스로 선언해서 사용한다**
- 주로 `private`으로 선언해 클래스 내부에서만 사용할 수 있도록 선언을 하는데, `private`이 아니면 외부에서도 사용할 수 있다

### 장점
- 내부 클래스에서 외부 클래스의 멤버들을 쉽게 접근할 수 있다
- 외부에는 불필요한 클래스를 감춤으로써(캡슐화) 코드의 복잡성을 줄일 수 있다

## 내부 클래스의 종류
- 인스턴스 내부 클래스
- 정적(static) 내부 클래스
- 지역(local) 내부 클래스
- 익명(anonymous) 내부 클래스

익명 내부 클래스를 제외하고는 변수와 같이 선언 위치에 따라 구분된다. 유효범위와 성질 또한 변수와 유사하다.

### 인스턴스 내부 클래스
- 외부 클래스의 멤버변수 선언 위치에 선언
- 주로 외부 클래스의 인스턴스 멤버들과 관련된 작업에 사용될 목적으로 선언된다
- private으로 선언하는 것을 권장
- 외부 클래스가 생성된 후 생성됨
- private이 아닌 내부 클래스는 다른 외부 클래스에서 생성할 수 있음
```java
public class Out {

	private int num = 10;
	private static int sNum = 20;

	// 보통 외부 클래스에 내부 클래스 타입의 변수를 선언한 뒤
	// 생성자에서 in = new In(); 으로 내부 클래스를 생성해준다
	private In in;

	public Out() {
		in = new In();
	}

	// 인스턴스 내부 클래스
	// 외부 클래스에서만 사용할거라면 private으로 선언을 많이한다
	private class In {
		int inNum = 100;

		// static int sInNum = 500; -> 오류가 발생한다
		// 왜냐하면 인스턴스 내부 클래스는 외부 클래스가 먼저 생성된 후에 생성된다
		// 하지만 static 변수는 외부 클래스와 상관없이 사용하겠다는 의미이기 때문에 오류가 발생한다
		// static 내부 클래스에서는 static 변수를 사용할 수 있다

		void inTest() {
			// 내부 클래스에서 외부 클래스의 private 변수를 사용할 수 있다
			System.out.println("OutClass num = " + num + "(외부 클래스의 인스턴스 변수)");

			// static 변수는 인스턴스 보다 먼저 생성되기 때문에 사용할 수 있다
			System.out.println("OutClass sNum = " + sNum + "(외부 클래스의 스태틱 변수)");

			System.out.println("InClass inNum = " + inNum + "(내부 클래스의 인스턴스 변수)");
		}
	}

	// private 아닌 인스턴스 내부 클래스
	// 외부 클래스가 아닌 다른 외부에서도 생성할 수 있다 -> but, 이렇게 쓰는 일의 거의 없다
	// 외부 클래스를 사용해서 이 내부 클래스를 생성한다
	// new Out.In2() 이런 생성자는 없고 이미 만들어진 Out 클래스를 사용해서 생성해야 한다
	class In2 {
		int inNum = 100;

		void inTest() {
			System.out.println("OutClass num = " + num + "(외부 클래스의 인스턴스 변수)");
			System.out.println("OutClass sNum = " + sNum + "(외부 클래스의 스태틱 변수)");
			System.out.println("InClass inNum = " + inNum + "(내부 클래스의 인스턴스 변수)");
		}
	}

	public void usingInClass() {
		// 외부 클래스 메서드에서 내부 클래스의 메서드를 사용
		in.inTest();
	}
}
```
```java
class OutTest {

	@Test
	void privateInClassTest() {
		Out out = new Out();
		out.usingInClass();
	}

	@Test
	void defaultInClassTest() {
		Out out = new Out();
		Out.In2 in = out.new In2();
		in.inTest();
	}

}
```

결과

```text
OutClass num = 10(외부 클래스의 인스턴스 변수)
OutClass sNum = 20(외부 클래스의 스태틱 변수)
InClass inNum = 100(내부 클래스의 인스턴스 변수)
```

### 정적 내부 클래스
- 외부 클래스의 멤버변수 선언 위치에 선언
- 외부 클래스 생성과 무관하게 사용할 수 있음
- 주로 외부 클래스의 static 멤버, static 메서드에서 사용될 목적으로 선언된다

```java
public class Out {

	private int num = 10;
	private static int sNum = 20;

	static class StaticInner {
		int inNum = 10;;
		static int sInNum = 200;

		void staticInnerTest() {
			// 외부 클래스의 생성과 상관없이 만들어질 수 있는 내부 클래스이기 때문에 외부 클래스의 인스턴스 변수에 접근할 수 없다
			// System.out.println("OutClass num = " + num + "(외부 클래스의 인스턴스 변수)");
			System.out.println("OutClass sNum = " + sNum + "(외부 클래스의 스태틱 변수)");
			System.out.println("InClass inNum = " + inNum + "(내부 클래스의 인스턴스 변수)");
			System.out.println("InClass sInNum = " + sInNum + "(내부 클래스의 스태틱 변수)");
		}

		static void staticInnerStaticMethodTest() {
			System.out.println("OutClass sNum = " + sNum + "(외부 클래스의 스태틱 변수)");
			// static 메서드는 static 내부 클래스의 생성과 상관없이 호출될 수 있기 때문에 static 내부 클래스의 인스턴스 변수에 접근할 수 없다
			// System.out.println("InClass inNum = " + inNum + "(내부 클래스의 인스턴스 변수)");
			System.out.println("InClass sInNum = " + sInNum + "(내부 클래스의 스태틱 변수)");
		}
	}
}
```
```java
class OutTest {

	@Test
	void staticInnerClassTest() {
		Out.StaticInner staticInner = new Out.StaticInner();
		staticInner.staticInnerTest();

		Out.StaticInner.staticInnerStaticMethodTest();
	}

}
```

### 지역 내부 클래스
- 외부 클래스의 메서드 내부나 초기화 블럭 안에 선언
- 선언된 영역 내부에서만 사용될 수 있다
- 메서드의 지역 변수나 매개 변수는 메서드 호출 이후에도 지역 내부 클래스에서 사용하는 경우가 있을 수 있으므로 final로 선언됨

```java
public class LocalAndAnonymousInnerClassOuter {

	int outNum = 100;
	static int sNum = 200;

	// 메서드의 매개변수는 stack 메모리에 생성
	Runnable getRunnable(int i) {

		// stack 메모리에 생성
		int num = 10;

		// getRunnable() 내부에 있는 지역 내부 클래스
		class MyRunnable implements Runnable {

			int localNum = 1000;

			@Override
			public void run() {

				// i = 50;
				// num = 20;
				/**
				 * 메서드의 지역변수(i, num)을 get하는건 가능하지만, set하는건 불가능하다
				 * set 하려고 하면 이런 메시지가 나온다 -> Variable 'i' is accessed from within inner class, needs to be final or effectively final
				 * 왜냐하면 getRunnable() 메서드의 지역변수 생명주기와 MyRunnable 클래스의 생명주기가 달라서 그렇다
				 * i와 num은 stack 메모리에 생성되었다가 메서드가 종료되면 메모리에서 제거된다
				 * but, MyRunnable 클래스는 생성되면 나중에 또 호출될 수 있다
				 * 또 호출되었을 때 i, num이 없으면 값을 할당해줄 수 없게 된다 -> 그럼 i, num은 stack에 생성되면 안된다 -> 그래서 이런 경우에 컴파일러가 다 final로 처리해버린다
				 * 따라서 내부 클래스에서 접근하는 변수들은 stack에 생성되고, 상수화돼서 상수 메모리에 생성된다 -> 그렇기 때문에 값을 변경할수는 없는 것이다
				 */

				System.out.println("i = " + i);
				System.out.println("num = " + num);
				System.out.println("localNum = " + localNum);

				// 지역 내부 클래스는 외부 클래스가 생성된 다음 불리기 때문에 외부 클래스 인스턴스 변수에 접근 가능
				System.out.println("outNum = " + outNum + "(외부 클래스 인스턴스 변수)");
				System.out.println("LocalAndAnonymousInnerClassOuter.sNum = "
						+ LocalAndAnonymousInnerClassOuter.sNum + "(외부 클래스 정적 변수)");
			}
		}

		return new MyRunnable();
	}
}
```
```java
class LocalAndAnonymousInnerClassOuterTest {

	@Test
	void localInnerClassTest() {
		LocalAndAnonymousInnerClassOuter outer = new LocalAndAnonymousInnerClassOuter();
		Runnable runnable = outer.getRunnable(100);

		runnable.run();
	}

}
```

결과
```text
i = 100
num = 10
localNum = 1000
outNum = 100(외부 클래스 인스턴스 변수)
LocalAndAnonymousInnerClassOuter.sNum = 200(외부 클래스 정적 변수)
```

### 익명 내부 클래스
- [지역 내부 클래스](#지역-내부-클래스)의 예에서 `MyRunnable` 클래스의 이름으로 외부에서 호출되는 경우가 없다 → `getRunnable()`이 호출되면 그 안에서 딱 한번 호출된다 → 클래스의 이름을 생략하자!
- 클래스의 이름을 생략하고 주로 하나의 인터페이스나 하나의 추상 클래스를 구현해 반환하는 경우가 많다
- 인터페이스나 추상 클래스 자료형의 변수에 직접 대입하여 클래스를 생성하거나 지역 내부 클래스의 메서드 내부에서 생성하여 반환할 수 있음

```java
public class LocalAndAnonymousInnerClassOuter {

	int outNum = 100;
	static int sNum = 200;

	Runnable getRunnableUsingAnonymousInnerClass(int i) {

		int num = 10;

		// 익명 내부 클래스 : 클래스의 이름(MyRunnable 같은)을 쓸 일이 없기 때문에 클래스의 이름을 없애고 Runnable 인터페이스를 구현한 객체를 바로 리턴
		return new Runnable() {

			int localNum = 1000;

			// 여전히 i, num을 변경하는 것은 불가능

			@Override
			public void run() {
				System.out.println("i = " + i);
				System.out.println("num = " + num);
				System.out.println("localNum = " + localNum);

				// 지역 내부 클래스는 외부 클래스가 생성된 다음 불리기 때문에 외부 클래스 인스턴스 변수에 접근 가능
				System.out.println("outNum = " + outNum + "(외부 클래스 인스턴스 변수)");
				System.out.println("LocalAndAnonymousInnerClassOuter.sNum = "
						+ LocalAndAnonymousInnerClassOuter.sNum + "(외부 클래스 정적 변수)");
			}
		};
	}

	// 이런 사용도 가능
	// 원래 Runnable을 구현한 어떤 클래스를 정의해야 하는데(내부 클래스) 이름 없이 정의했기 때문에 이것도 익명 내부 클래스
	Runnable runnable = new Runnable() {
		@Override
		public void run() {
			System.out.println("Runnable class");
		}
	};
}
```
```java
class LocalAndAnonymousInnerClassOuterTest {

	@Test
	void anonymousInnerClassTest() {
		LocalAndAnonymousInnerClassOuter outer = new LocalAndAnonymousInnerClassOuter();
		Runnable runnable = outer.getRunnableUsingAnonymousInnerClass(100);

		runnable.run();

		outer.runnable.run();
	}

}
```

<br/>

---

<br/>

출처 및 참고
- [패스트캠퍼스 - 한번에 끝내는 Java/Spring 웹 개발 마스터 초격차 패키지 Online](https://fastcampus.co.kr/dev_online_javaend)
- Java의 정석(남궁 성)