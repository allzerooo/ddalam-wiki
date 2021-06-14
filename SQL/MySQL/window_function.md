# 윈도우 함수(Window Function)
- 데이터를 그룹화해 집계해준다
- 기존 데이터 + 집계된 값을 함께 나타낼 수 있다

## 형태
```sql
함수(함수적용열) OVER (PARTITION BY 그룹열 ORDER BY 순서열)
```

## 데이터 위치 바꾸기

### `LAG`
데이터를 N칸 미루기
```sql
LAG(열, N, 채울 값) OVER (PARTITION BY 그룹열 ORDER BY 순서열)
```

### `LEAD`
데이터를 N칸 당기기
```sql
LEAD(열, N, 채울 값) OVER (PARTITION BY 그룹열 ORDER BY 순서열)
```



