# 데이터베이스 면접 질문 및 답변

## Q1. 유일성과 최소성에 대해 설명하시오.
- **유일성:** 테이블 내에서 동일한 값이 존재하지 않음.
- **최소성:** 최소한의 속성으로 유일성을 보장.

---

## Q2. 외래키 설명해보시오.
- 개체 관계를 식별하는 데 사용됩니다.
- 테이블 간의 관계를 연결하는 역할을 합니다.

---

## Q3. MySQL의 커넥션 풀(Connection Pool)이란 무엇인가요? 어떤 장점이 있나요?
- **정의:** 미리 커넥션을 생성하고 요청이 들어오면 제공하는 기능.
- **장점:** 커넥션 생성/해제 비용을 절감하여 성능을 향상시킵니다.

---

## Q4. InnoDB MVCC란 무엇인가요?
- **MVCC (Multi-Version Concurrency Control):** 다중 버전 동시성 제어.
    - 쓰기 시: 이전 버전을 복사(스냅샷)하여 새로운 버전을 생성.
    - 읽기 시: 이전 버전을 제공.

---

## Q5. 기본키(Primary Key)와 유니크 키(Unique Key)의 차이점은 무엇인가요?
- **기본키:**
    - NULL 값 불가.
    - 테이블당 하나만 설정 가능.
- **유니크 키:**
    - NULL 값을 하나 허용.
    - 여러 개 설정 가능.

---

## Q6. InnoDB의 클러스터형 인덱스(Clustered Index)란 무엇인가요?
- 기본키를 기준으로 데이터가 정렬되어 있어 검색 속도가 빠릅니다.

---

## Q7. 기본키(Primary Key)의 주요 특징은 무엇인가요?
- 각 행을 유일하게 식별할 수 있는 키입니다.
- **NULL 값을 가질 수 없고**, 테이블당 하나의 기본키만 설정 가능합니다.
- 복합키 형태로도 사용 가능합니다.

---

## Q8. 무결성의 종류에 대해서 설명해주세요.
1. **개체 무결성:** 각 행이 고유해야 하며, 기본키는 NULL 값을 가질 수 없습니다.
2. **도메인 무결성:** 속성이 정해진 도메인 내에 있어야 합니다.
3. **참조 무결성:** 외래키가 참조하는 부모 테이블의 기본키와 일치해야 하고 관계를 유지해야 합니다.

---

## Q9. 키의 종류와 정의
1. **기본키 (Primary Key):**
    - 테이블의 주 식별자로, 유일성과 최소성을 만족.
    - 중복되지 않으며 NULL 값을 허용하지 않습니다.
2. **외래키 (Foreign Key):**
    - 다른 테이블의 기본키를 참조하여 테이블 간의 관계를 형성.
    - NULL 값을 허용하며, 테이블 내에서 중복될 수 있습니다.
3. **후보키 (Candidate Key):**
    - 유일성과 최소성을 만족하는 속성.
4. **대체키 (Alternate Key):**
    - 후보키 중 기본키로 선택되지 못한 속성.
5. **슈퍼키 (Super Key):**
    - 유일성을 만족하는 속성의 집합.

---

## Q10. 데이터 무결성과 데이터 정합성
- **무결성 (Integrity):** 데이터의 정확성과 일관성을 유지하는 것.
- **정합성 (Consistency):** 데이터 간의 값이 서로 일치하는 것.

### 차이점:
- 정합성은 데이터가 일치하는지 여부를 말하며, 무결성은 데이터의 정확성과 일관성을 포함합니다.
- 데이터는 정합성이 있을 수 있지만 무결성이 훼손될 수 있습니다.  
  예: 중복 데이터가 동일한 값을 가지는 경우 (정합성 O, 무결성 X).

---

## Q11. MySQL의 기본 포트는 무엇인가요?
- **3306**입니다.

---

## Q12. MySQL 서버 구조
1. **MySQL 엔진:**
    - SQL 처리 및 최적화.
    - 보조 메모리 통신과 같은 데이터베이스의 일반적인 기능 수행.
2. **스토리지 엔진:**
    - 디스크에 실제 데이터를 저장하거나 읽는 역할.