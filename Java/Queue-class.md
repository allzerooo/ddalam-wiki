# Queue Class

### 선언

```java
import java.util.LinkedList;
import java.util.Queue;
Queue<Integer> queue = new LinkedList<>();
Queue<String> queue = new LinkedList<>();
```

### 주요 메서드

```java
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);     // 값 넣기
queue.peek();       // 첫번째 값 확인하기
queue.poll();       // 첫번째 값을 반환 + 제거, 비어있으면 null
queue.remove();     // 첫번째 값 제거
queue.size();       // 크기 확인
queue.clear();      // 비우기
queue.isEmpty();    // 비어있는지 확인
```

---
