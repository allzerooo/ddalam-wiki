# Effectvie Java

Effective Java(조슈아 블로크)를 읽고, 정리

<br/>

- [객체 생성과 파괴 - 아이템7. 다 쓴 객체 참조를 해제하라](#item7)
- [객체 생성과 파괴 - 아이템8. finalizer와 cleaner 사용을 피하라](#item8)
- [객체 생성과 파괴 - 아이템9. try-finally 보다는 try-with-resources를 사용하라](#item9)
- [모든 객체의 공통 메서드 - 아이템10. equals는 일반 규약을 지켜 재정의하라](#item10)
- [모든 객체의 공통 메서드 - 아이템11. equals를 재정의하려거든 hashCode도 재정의하라](#item11)

<br/>

## 객체 생성과 파괴

### <a name="itme7"></a>아이템 7. 다 쓴 객체 참조를 해제하라

가비지 컬렉터는 다 쓴 객체의 참조를 해제하지 않았을 때 해당 참조가 유효한지 알 수 없는 경우가 있다. 따라서, 해당 참조를 다 썼을 때 null 처리를 해줘야 된다. 하지만 모든 객체를 다 쓰자마자 일일이 null 처리를 할 필요는 없다. 객체 참조를 null 처리해야 되는 경우는 대표적으로 다음 3가지 경우이다.

1. 자기 메모리를 직접 관리하는 클래스
2. 객체 참조를 캐시에 넣고 사용했을 때
3. 클라이언트가 콜백(callback)을 등록했을 때

<br/>

### <a name="itme8"></a>아이템 8. finalizer와 cleaner 사용을 피하라

<br/>

### <a name="itme9"></a>아이템 9. try-finally 보다는 try-with-resources를 사용하라

`try-with-resources` 구조를 사용하려면 닫아야 하는 자원 클래스는 반드시 `AutoCloseable` 인터페이스를 구현해야 한다.
(AutoCloseable 인터페이스는 void를 반환하는 `close` 메서드 하나만 정의된 인터페이스다)

```java
static void copy(String src, String dst) {
    try (InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        }
}
```

`close`는 코드에 나타나지 않지만, `close`에서 발생한 예외는 숨겨지고(스택 추적 내역에서 찾을 수 있고, `Throwable`의 `getSuppressed` 메서드를 이용하면 프로그램 코드에서 가져올 수 있다) try문 안에서 발생한 예외가 기록된다.
`try-with-resources`에서도 예외는 던질 수 있고, `catch` 절을 쓸 수도 있다.

<br/>
<br/>

## 모든 객체의 공통 메서드

`Object`클래스의 `final`이 아닌 메서드(`equals`, `hashCode`, `toString`, `clone`, `finalize`)는 모두 재정의(`overriding`)를 염두에 두고 설계되었고, 재정의 시 지켜야 하는 규약이 명확히 정의되어 있다. 그래서 `Object를` 상속하는 모든 클래스는 이 메서드들을 규약에 맞게 재정의해야 한다. 메서드를 잘못 구현하면 클래스가 이 규약을 준수한다고 가정하는 클래스를 오작동하게 만들 수 있다

<br/>

### <a name="itme10"></a>아이템 10. equals는 일반 규약을 지켜 재정의하라

재정의하지 않아도 되는 경우

- 클래스가 값을 표현하는게 아니라 동작하는 개체를 표현하는 클래스일 때 (ex. Thread. Object의 equals 메서드는 이러한 클래스에 적합하게 구현되었다)
- 물리적으로 같은가가 아닌 논리적으로 같은가를 검사할 일이 없을 때 (ex. java.util.regex.Pattern은 equals를 재정의해서 두 Pattern의 인스턴스가 같은 정규표현식을 나타내는지 검사한다)
- 상위 클래스에서 재정의한 `equals`가 하위 클래스에도 딱 들어맞을 때 (ex. Set의 구현체는 AbstractSet이 구현한 equals를 상속받아 쓰고, List 구현체는 AbstractList로 부터, Map 구현체는 AbstractMap으로부터 상속받아 그대로 쓴다)
- 클래스가 `private`이거나 `package-private`이고 `equals` 메서드를 호출할 일이 없을 때

<br/>

재정의해야 하는 경우

- 두 객체가 논리적으로 같은가를 확인해야 하는데 상위 클래스의 `equals`가 논리적 동치성을 비교하도록 재정의되지 않았을 때 (주로 값 클래스들이 해당됨)

<br/>

`eqauls` 메서드를 재정의할 때 지켜야 하는 규약

```
null이 아닌 모든 참조값 x, y, z에 대해
x.eqauls(x)는 true
x.eqauls(y)가 true면 y.equals(x)도 true
x.equals(y)가 true이고, y.equals(z)도 true면, x.eqauls(z)도 true
x.equals(y)를 반복해서 호출하면 항상 true 또는 항상 false를 반환
x.equals(null)은 false
```

<br/>

### <a name="itme11"></a>아이템11. equals를 재정의하려거든 hashCode도 재정의하라

논리적으로 같은 객체는 같은 해시코드를 반환해야 한다. equals는 물리적으로 다른 두 객체를 논리적으로 같다고 할 수 있지만 Object의 기본 hashCode 메서드는 두 객체를 다르다고 판단한다. 따라서 equals를 재정의할 때는 hashCode도 반드시 재정의해야 한다.

<br/>

hashCode를 재정의 할 때 지켜야 하는 규약

- equals 비교에 사용되는 정보가 변경되지 않았다면 hashCode 메서드는 몇 번을 호출해도 항상 같은 값을 반환해야 한다. 단, 애플리케이션을 다시 실행한다면 달라져도 상관없다.
- equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode도 같은 값이어야 한다.
- equals(Object)가 두 객체를 다르다고 판단했더라도 두 객체의 hashCode가 다른 값일 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.

<br/>

좋은 해시 함수

- 서로 다른 인스턴스에 다른 해시코드를 반환한다(3번 규약)
- 이상적인 해시 함수는 주어진 서로 다른 인스턴스들을 32비트 정수 범위에 균일하게 분배해야 한다

<br/>

좋은 hashCode 작성 방법
