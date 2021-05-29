## Object Assertions

### 1. `isEqualTo()` : 객체의 참조를 비교
```java
@Test
    void compareObjects() {
        Dog fido = new Dog("Fido", 5.25F);
        Dog fidosClone = new Dog("Fido", 5.25F);

        // isEqualTo()는 객체의 참조를 비교하기 때문에 실패
        assertThat(fido).isEqualTo(fidosClone);
```

<br/>

### 2. `isEqualToComparingFieldByFieldRecursively()` : 객체의 필드를 재귀적으로 비교
```java
@Test
    void compareObjects() {
        Dog fido = new Dog("Fido", 5.25F);
        Dog fidosClone = new Dog("Fido", 5.25F);

        // 객체의 필드를 재귀적으로 비교하기 때문에 성공
        assertThat(fido).isEqualToComparingFieldByFieldRecursively(fidosClone);
    }
```

### 더 다양한 객체 비교
[AbstractObjectAssert](https://joel-costigliola.github.io/assertj/core-8/api/org/assertj/core/api/AbstractObjectAssert.html)