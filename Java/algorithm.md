# Algorithm

### DFS

```java
public static void dfs(int i) {
    visit[i] = true;
    System.out.print(i + " ");

    for (int j = 1; j < n+1; i++) {
        if (map[i][j] == 1 && visit[j] == false) {
            dfs(j);
        }
    }
}
```

<br/>

### BFS

```java
public static void bfs(int i) {
    Queue<Integer> queue = new LinkedList<>();
    queue.offer(i);
    visit[i] = true;

    while (!queue.isEmpty()) {
        int temp = queue.poll();
        System.out.print(temp + " ");

        for (int j = 1; j < n+1; j++) {
            if (map[temp][j] == 1 && visit[j] == false) {
                queue.offer(j);
                visit[j] = true;
            }
        }
    }
}
```
