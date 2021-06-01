### 배열 반환 값 테스트
1. `containsExactly()` 사용
    ```java
    @Test
    void 배열_반환_값_테스트() {
        String[] actual = "a,b".split(",");
        assertThat(actual).containsExactly("a", "b");
    }
    ```
2. `contains()` 사용
   ```java
   @Test
    void 배열_반환_값_테스트() {
        String[] actual = "a,b".split(",");
        assertThat(actual).contains("a");
    }
   ```