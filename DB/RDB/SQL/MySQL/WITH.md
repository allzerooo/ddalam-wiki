
# WITH 절

- CTE, Common Table Expression
- 항상 제일 먼저 실행되어 이름을 가지는 임시 테이블로 저장된다
- WITH 절로 만들어진 임시 테이블은 단독으로 조회되거나 조인되는 테이블로 활용된다
- SQL 문장 내에서 한 번 이상 사용될 수 있으며 SQL 문장이 종료되면 자동으로 CTE 임시 테이블은 삭제된다
- CTE는 재귀적 반복 실행 여부를 기준으로 Non-recursive와 Recursive CTE로 구분된다
- MySQL의 CTE는 재귀 여부에 관계없이 다양한 SQL 문장에서 사용할 수 있다
  