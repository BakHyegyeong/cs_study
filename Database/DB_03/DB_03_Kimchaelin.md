# SQL의 DDL, DML, DCL, TCL의 정의와 역할

---

## **1. DDL (Data Definition Language)**
- **데이터 정의어**: 데이터베이스의 구조를 정의하거나 관리하는 역할.
- **종류**:
  - **CREATE**: 테이블, 인덱스, 뷰 등의 객체를 생성.
  - **ALTER**: 기존 객체를 수정.
  - **DROP**: 객체 삭제.
  - **TRUNCATE**: 테이블 데이터를 초기화(구조는 유지).
- **역할**:
  - 데이터베이스 객체의 생성, 수정, 삭제.
  - 데이터베이스의 구조(스키마)를 정의.

---

## **2. DML (Data Manipulation Language)**
- **데이터 조작어**: 데이터베이스에 저장된 데이터를 조작하는 역할.
- **종류**:
  - **SELECT**: 조회.
  - **INSERT**: 삽입.
  - **UPDATE**: 수정.
  - **DELETE**: 삭제.
- **역할**:
  - 데이터베이스 내 데이터를 조회, 삽입, 수정, 삭제.

---

## **3. DCL (Data Control Language)**
- **데이터 제어어**: 데이터베이스 사용자와 권한 관리를 담당.
- **종류**:
  - **GRANT**: 권한 부여.
  - **REVOKE**: 권한 회수.
- **역할**:
  - 사용자에게 특정 데이터베이스 객체에 대한 권한을 부여하거나 회수.

---

## **4. TCL (Transaction Control Language)**
- **트랜잭션 제어어**: 데이터베이스의 트랜잭션을 제어.
- **종류**:
  - **COMMIT**: 트랜잭션에서 수행된 작업을 저장.
  - **ROLLBACK**: 트랜잭션 작업을 취소하고 이전 상태로 되돌림.
  - **SAVEPOINT**: 트랜잭션 내에서 특정 지점을 저장.
- **역할**:
  - 트랜잭션의 작업 상태를 제어하고 데이터 무결성을 보장.

---

# INNER JOIN, OUTER JOIN, CROSS JOIN의 차이점

---

## **1. 조인이란?**
- 두 개 이상의 테이블을 연결하여 하나의 결과 집합을 생성하는 작업.
- 테이블 간의 관계를 기반으로 데이터를 결합.

---

## **2. INNER JOIN**
![INNER JOIN](https://hongong.hanbit.co.kr/wp-content/uploads/2021/11/%ED%98%BC%EC%9E%90-%EA%B3%B5%EB%B6%80%ED%95%98%EB%8A%94-SQL_INNER-JOIN-600x600.png)

- **정의**: 두 테이블 간의 **JOIN 조건을 만족**하는 데이터(교집합)만 반환.
- **특징**:
  - 조건에 일치하지 않는 데이터는 제외.
  - 가장 기본적인 JOIN 형태.

---

## **3. OUTER JOIN**
![OUTER JOIN](https://hongong.hanbit.co.kr/wp-content/uploads/2021/11/OUTER-JOIN_%EB%8D%94%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-600x600.png)

- **정의**: 두 테이블 간의 데이터를 조건에 따라 연결하며, 조건에 맞지 않는 데이터도 포함하여 반환.
- **종류**:
  - **LEFT OUTER JOIN**:
    - 왼쪽 테이블의 모든 데이터와 조건에 맞는 오른쪽 테이블의 데이터를 반환.
    - 오른쪽 테이블에 일치하는 데이터가 없으면 **NULL**로 표시.
  - **RIGHT OUTER JOIN**:
    - 오른쪽 테이블의 모든 데이터와 조건에 맞는 왼쪽 테이블의 데이터를 반환.
    - 왼쪽 테이블에 일치하는 데이터가 없으면 **NULL**로 표시.
  - **FULL OUTER JOIN**:
    - 왼쪽 테이블과 오른쪽 테이블의 **모든 데이터**를 반환.
    - 양쪽 테이블에서 일치하지 않는 데이터는 **NULL**로 채워짐.

- **차집합 구하기**(LEFT OUTER JOIN Only(LEFT JOIN+Where 조건))
  - LEFT OUTER JOIN 후 **WHERE 조건**으로 오른쪽 테이블의 값이 NULL인 데이터를 선택.

---

## **4. CROSS JOIN**
![CROSS JOIN](https://hongong.hanbit.co.kr/wp-content/uploads/2021/11/%ED%98%BC%EC%9E%90-%EA%B3%B5%EB%B6%80%ED%95%98%EB%8A%94-SQL_CROSS-JOIN-600x600.png)

- **정의**: 두 테이블의 모든 행을 조합하여 반환(곱집합).
- **특징**:
  - 결과는 **A 테이블의 행 수 × B 테이블의 행 수** 만큼 생성.
  - 일반적으로 다른 조건과 함께 사용하지 않으면 불필요하게 큰 결과가 생성됨.

## 조인과 서브 쿼리의 차이
- **조인**: 테이블을 여러 개를 결합시에 where 절에 조건을 통해 대규모 조합이 가능. 또한 속도가 빠름
- **서브 쿼리**: 보다 자유로운 쿼리를 할 수 있다. 속도가 느림.
