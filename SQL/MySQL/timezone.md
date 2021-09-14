### 데이터베이스의 타임존 확인

```sql
SELECT @@GLOBAL.time_zone, @@SESSION.time_zone, @@system_time_zone;
```

- `SYSTEM` 타임존이 설정되어 있지 않다는 뜻으로 시스템의 타임존을 사용한다