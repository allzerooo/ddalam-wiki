- [내부 클래스 (inner class)](#내부-클래스-inner-class)
	- [내부 클래스의 종류](#내부-클래스의-종류)
		- [인스턴스 내부 클래스](#인스턴스-내부-클래스)
		- [정적 내부 클래스](#정적-내부-클래스)

# 내부 클래스 (inner class)
- 클래스 내부에 선언한 클래스로 외부 클래스와 밀접한 연관이 있는 경우가 많고, 다른 외부 클래스에서 사용할 일이 거의 없는 경우에 내부 클래스로 선언해서 사용한다
- 주로 `private`으로 선언해 클래스 내부에서만 사용할 수 있도록 선언을 하는데, `private`이 아니면 외부에서도 사용할 수 있다
- 중첩 클래스라고도 함

## 내부 클래스의 종류
- 인스턴스 내부 클래스
- 정적(static) 내부 클래스
- 지역(local) 내부 클래스
- 익명(anonymous) 내부 클래스

익명 내부 클래스를 제외하고는 변수와 동일하게 생각하면 된다.

### 인스턴스 내부 클래스
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
- 외부 클래스 생성과 무관하게 사용할 수 있음
- 정적 변수, 정적 메서드 사용

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