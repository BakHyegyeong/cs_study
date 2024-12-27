# 인덱스

추가적인 쓰기 작업과 저장 공간을 활용하여 데이터베이스 테이블의 **검색 속도를 향상시키기 위한 자료구조**
→ 인덱스가 없을 경우 full table scan을 진행해야 하기 때문에 처리 속도가 현저히 떨어지게 된다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcBQD97%2FbtqKRtpm2pl%2Frmo7jTbiiE9tsSQsUg0JPK%2Fimg.png)

## 인덱스의 관리

**최신의 정렬된 상태로 유지해야** 원하는 값을 빠르게 탐색할 수 있기 때문에 인덱스가 적용된 column에 아래의 query가 수행될 경우 **추가적인 연산이 필요하며 그에 따른 오버헤드가 발생**한다.
→ 따라서 CREATE, DELETE, UPDATE가 빈번한 속성에 인덱스를 걸 경우 성능이 저하된다.

- INSERT: 새로운 데이터에 대한 인덱스를 추가한다.
- DELETE: 삭제하는 데이터의 인덱스를 사용하지 않는다는 작업을 진행한다.
- UPDATE : 기존의 인덱스를 사용하지 않음 처리하고 갱신된 데이터에 대한 인덱스를 추가한다.

### 인덱스의 장점

- 테이블을 조회하는 속도와 그에 따른 성능을 향상시킬 수 있다.
- 전반적인 시스템의 부하를 줄일 수 있다.
- 대수확장성 : 트리 깊이가 리프 노드 수에 비해 매우 느리게 성장하는 것
(인덱스가 한 깊이 증가할 때마다 최대 인덱스 항목의 수는 4배씩 증가한다.)

### 인덱스의 단점

- 인덱스를 관리하기 위해 DB의 약 10%에 해당하는 저장공간이 필요하다.
- 인덱스를 관리하기 위해 추가 작업이 필요하다.
- 인덱스를 잘못 사용할 경우 오히려 성능이 저하되는 역효과가 발생할 수 있다.
→ 따라서 인덱스는 아래의 경우에 적합하다.
    - 규모가 큰 테이블
    - INSERT, UPDATE, DELETE 가 자주 발생하지 않는 column
    - JOIN이나 WHERE, ORDER BY에 자주 사용되는 column
    - 데이터의 중복도가 낮은 column
    - 검색 빈도가 높은 column

## 인덱스를 만드는 방법

- 단일 인덱스(클러스터형 인덱스)

```sql
CREATE TABLE table1(
    uid INT(11) NOT NULL auto_increment,
    id VARCHAR(20) NOT NULL,
    name VARCHAR(50) NOT NULL,
    address VARCHAR(100) NOT NULL,
    PRIMARY KEY('uid'),
    key idx_name(name),       // 인덱스 생성
    key idx_address(address)  // 인덱스 생성
)

-- 단일 인덱스
CREATE INDEX 인덱스이름 ON 테이블이름(필드이름1)
CREATE UNIQUE INDEX 인덱스 이름 ON 테이블이름(필드이름1)
```

- 다중 컬럼 인덱스 (세컨더리 인덱스)

```sql
CREATE TABLE table2(
    uid INT(11) NOT NULL auto_increment,
    id VARCHAR(20) NOT NULL,
    name VARCHAR(50) NOT NULL,
    address VARCHAR(100) NOT NULL,
    PRIMARY KEY('uid'),
    key idx_name(name, address)    // 인덱스 생성
)

-- 다중 컬럼 인덱스
CREATE INDEX 인덱스이름 ON 테이블이름(필드이름1, 필드이름2, ...)
CREATE UNIQUE INDEX 인덱스 이름 ON 테이블이름(필드이름1, 필드이름2, ...)
```

- 인덱스 사용 방법

```sql
-- 인덱스 조회 
SELECT * FROM table1 WHERE name='홍길동' AND address='경기도';
```

: 일반적인 query문과 같지만 다중 컬럼 인덱스일 경우 **인덱스로 설정해준 제일 왼쪽 column이 WHERE절에 반드시 사용**되어야 한다. <br>
→  key idx_name(name, address, age) 일때 
WHERE name = ? AND address = ? 는 인덱스가 적용되지만 
WHERE name = ? AND age = ? 에서 age 컬럼은 인덱스 적용이 되지 않는다.

## 인덱스 선정 기준

- 규모가 큰 테이블
- INSERT, UPDATE, DELETE 가 자주 발생하지 않는 column
- JOIN이나 WHERE, ORDER BY에 자주 사용되는 column
- 데이터의 중복도가 낮은 column
- 검색 빈도가 높은 column

### 복합 인덱스 선정 기준

- 어떠한 값과 같음을 비교하는 ==이나 equal이라는 쿼리가 있다면 제일 먼저 인덱스로 설정한다.
- 정렬에 쓰는 필드라면 그 다음 인덱스로 설정한다.
- 다중 값을 출력해야 하는 필드, 즉 쿼리 자체가 > 이거나 < 등 많은 값을 출력해야 하는 쿼리에 쓰이는 필드라면 나중에 인덱스로 설정한다.
- 카디널리티가 높은 순서를 기반으로 인덱스를 생성한다.


> 💡 카디널리티 : 유니크한 값의 정도


---

# 실행 계획

**사용자가 요청한 SQL을 최적으로 수행**하고자 옵티마이저가 수립한 일련의 처리 절차를 트리구조로 표현한 것

→ SQL이 실행되는데 필요한 cost를 계산하고 그 cost를 통해 **어떤 방식으로 실행하는 것이 가장 좋은지를 판단한다.**

데이터베이스에서 전달된 SQL문은 **파싱 > 최적화 > 실행 과정**을 거쳐서 처리되는데 실행 계획은 이중 SQL 최적화 과정에 속한다.

- **SQL파싱** : SQL구문에 오류가 있는지, SQL을 실행해야 하는 대상 객체가 존재하는지를 검사
- **SQL최적화** : SQL이 실행되는데 필요한 cost를 계산, 실행 계획 작성
- **SQL실행** : 실행 계획을 바탕으로 메모리상에서 데이터를 읽거나 물리적인 공간에서 데이터를 로딩하는 등의 작업

## 옵티마이저

시스템 및 오브젝트 통계 정보를 바탕으로 **다양한 실행 경로를 생성하고 비교한 후 효율적인 것을 하나 선택**

1. 사용자로부터 전달 받은 쿼리를 수행하는데 사용될 후보군인 실행 계획들을 찾는다.

2. 데이터 딕셔너리에 미리 수집한 통계정보를 이용해서 각 실행 계획의 예상비용을 산정한다.

3. 최저 비용(cost)을 나타내는 실행 계획을 선택한다.

## 힌트

SQL이 복잡할수록 옵티마이저의 실행 계획보다 **개발자가 찾은 경로가 더 효율적일 수 있기에** 힌트를 사용해 **데이터 액세스 경로를 바꿀 수 있다.**

→ 옵티마이저의 실행 계획을 본 후 힌트를 사용해 경로를 바꿔야 한다.

```sql
select * from tbl_board order by bno desc;

// 힌트 적용
select /*+ INDEX_DESC (tbl_board tbl_board_pk)*/ * from tbl_board;
```

order by가 아닌 인덱스를 사용해 조회 성능을 높혔다.

### 힌트 종류

| **구분**              | **Hint**                          | **설명**                                                                 |
|------------------------|-----------------------------------|--------------------------------------------------------------------------|
| **최적화 목표**         | `/*+ ALL_ROWS */`                | 전체 처리속도 최적화                                                     |
|                        | `/*+ FIRST_ROWS(N) */`           | 최초 N건 응답속도 최적화                                                 |
|                        | `/*+ CHOOSE */`                  | 테이블의 통계정보 유무에 따라 규칙기반 또는 비용기반으로 최적화            |
|                        | `/*+ RULE */`                    | 규칙기반 옵티마이저를 이용 최적화                                         |
| **Index Scan 방식**     | `/*+ FULL(<Table명>) */`         | TABLE FULL SCAN 유도                                                     |
|                        | `/*+ INDEX(<Table> <Index>) */`  | INDEX SCAN 유도                                                          |
|                        | `/*+ NO_INDEX(<Index>) */`       | 지정한 인덱스 외 다른 인덱스 유도                                         |
|                        | `/*+ INDEX_DESC(<Table> <Index>) */` | INDEX를 역순으로 Scan하도록 유도                                      |
|                        | `/*+ INDEX_FFS(<Table> <Index>) */` | INDEX FAST FULL SCAN 유도                                              |
|                        | `/*+ INDEX_SS(<Table> <Index>) */` | INDEX SKIP SCAN 유도                                                   |
| **Join 순서**          | `/*+ ORDERED */`                 | FROM절에 나열된 순으로 Join                                              |
|                        | `/*+ LEADING(<Table1> <Table2> ..) */` | Hint 괄호 내 기술 순으로 Join                                        |
|                        | `/*+ SWAP_JOIN_INPUTS(<Table>) */` | HASH Join에서 BUILD INPUT 을 지정                                    |
| **Join 방식**          | `/*+ USE_NL(<Table>) */`          | NESTED LOOP Join을 지시                                                 |
|                        | `/*+ NO_USE_NL(<Table>) */`       | NESTED LOOP Join이 아닌 방식을 지시                                      |
|                        | `/*+ USE_NL_WITH_INDEX(<Table><Index>) */` | NESTED LOOP Join을 하면서 인덱스 사용 지시                         |
|                        | `/*+ USE_MERGE(<Table>) */`       | SORT MERGE Join을 지시                                                   |
|                        | `/*+ USE_HASH(<Table>) */`        | HASH Join을 지시                                                         |
|                        | `/*+ NO_USE_HASH(<Table>) */`     | HASH Join이 아닌 방식을 지시                                              |
| **Sub-Query 처리 방식** | `/*+ PUSH_SUBQ */`               | 메인쿼리부터 순서대로 진행되는 것이 아니라 서브쿼리가 먼저 처리되게 지시   |
|                        | `/*+ NO_UNNEST */`               | 서브쿼리를 Filter 방식으로 처리하게 지시                                  |
|                        | `/*+ UNNEST */`                  | Filter 방식인 아닌 조인 방식으로 처리하게 지시                            |
|                        | `/*+ NL_SJ */` (w/ UNNEST)       | EXIST, IN에서 NESTED LOOP Join SEMI 처리                                 |
|                        | `/*+ NL_AJ */` (w/ UNNEST)       | EXIST, IN에서 NESTED LOOP Join ANTI 처리                                 |
|                        | `/*+ HASH_SJ */` (w/ UNNEST)     | EXIST, IN에서 HASH Join SEMI 처리                                        |
|                        | `/*+ HASH_AJ */` (w/ UNNEST)     | EXIST, IN에서 HASH Join ANTI 처리                                        |
| **병렬 처리**           | `/*+ PARALLEL(<Table><병렬수>) */` | 병렬수 만큼 병렬처리 수행 지시                                         |
|                        | `/*+ NOPARALLEL(<Table>) */`      | 병렬처리 하지 않고 테이블 액세스 수행                                    |
|                        | `/*+ PQ_DISTRIBUTE(<Table>) */`   | 병렬처리 할당을 위한 힌트                                                |
|                        | `/*+ PARALLEL_INDEX(<Index><병렬수>) */` | 병렬수 만큼 인덱스 병렬처리 지시                                    |
|                        | `/*+ NOPARALLEL_INDEX(<Index>) */` | 병렬처리 하지 않고 인덱스 스캔 수행                                    |
