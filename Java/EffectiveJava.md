# Effectvie Java

Effective Java(조슈아 블로크)를 읽고, 정리

<br/>

- [객체 생성과 파괴 - 아이템7. 다 쓴 객체 참조를 해제하라](#item7)
- [객체 생성과 파괴 - 아이템8. finalizer와 cleaner 사용을 피하라](#item8)
- [객체 생성과 파괴 - 아이템9. try-finally 보다는 try-with-resources를 사용하라](#item9)

<br/>

## 객체 생성과 파괴

### <a name="itme7"></a>다 쓴 객체 참조를 해제하라

가비지 컬렉터는 다 쓴 객체의 참조를 해제하지 않았을 때 해당 참조가 유효한지 알 수 없는 경우가 있다. 따라서, 해당 참조를 다 썼을 때 null 처리를 해줘야 된다. 하지만 모든 객체를 다 쓰자마자 일일이 null 처리를 할 필요는 없다. 객체 참조를 null 처리해야 되는 경우는 대표적으로 다음 3가지 경우이다.

1. 자기 메모리를 직접 관리하는 클래스
2. 객체 참조를 캐시에 넣고 사용했을 때
3. 클라이언트가 콜백(callback)을 등록했을 때

<br/>

### <a name="itme8"></a>finalizer와 cleaner 사용을 피하라

<br/>

### <a name="itme9"></a>try-finally 보다는 try-with-resources를 사용하라

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
