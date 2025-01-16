# SELECT 처리 순서와 GROUP BY, ORDER BY의 실행 순서
### SELECT 처리 순서
FROM → ON → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT

### GROUP BY, ORDER BY의 실행 순서
GROUP BY → 집계 결과 생성 → ORDER BY로 정렬

# CASCADE와 참조 무결성의 개념
CASCADE는 **외래 키**가 참조하는 데이터가 삭제되거나 업데이트될 때, 참조하고 있는 데이터에도 영향을 미치는 동작을 나타낸다.
1. ON [DELETE/UPDATE] CASCADE : 부모 테이블의 데이터가 삭제/수정되면, 이를 참조하는 자식 테이블의 데이터도 삭제/수정

### **참조 무결성**
외래키가 참조하는 데이터가 삭제되거나 수정되더라도, 데이터의 관계가 깨지지 않도록 보장하는 규칙.

# DROP, TRUNCATE, DELETE의 차이점
### DROP
- 테이블 또는 데이터베이스 객체를 완전히 삭제.
- 테이블의 구조와 데이터, 인덱스 등 모든 정보가 삭제

### TRUNCATE
- 테이블의 모든 행을 삭제
- 테이블의 구조는 그대로 유지

### DELETE
- 테이블의 행을 삭제
- WHERE 절을 사용해 조건에 맞는 행만 삭제 가능
