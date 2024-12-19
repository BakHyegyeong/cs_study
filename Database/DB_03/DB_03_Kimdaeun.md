# SQL의 DDL, DML, DCL의 정의와 역할

SQL(Structured Query Language) : 데이터베이스를 관리하기 위해 사용되는 언어

## 1. DDL (Data Definition Language)

**DDL**은 데이터베이스의 **구조(스키마)를 정의하거나 수정**하는 데 사용

- 데이터베이스 및 테이블 생성, 수정, 삭제 등 **데이터 구조를 정의**
- 주로 데이터베이스 객체를 관리

### 명령어

- `CREATE` : 테이블, 데이터베이스, 뷰 등을 생성
- `ALTER` : 기존 테이블이나 데이터베이스 구조를 수정
- `DROP` : 데이터베이스 객체를 삭제
- `TRUNCATE` : 테이블의 모든 데이터를 삭제(테이블 구조는 유지)

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    email VARCHAR(100)
);

ALTER TABLE users ADD COLUMN phone VARCHAR(15);

DROP TABLE users;
```

## **2. DML (Data Manipulation Language)**

**DML**은 **데이터를 조작**하거나 **조회**하는 데 사용

- 테이블에 데이터를 **삽입, 수정, 삭제, 조회**
- 데이터의 상태를 변경

### **명령어**

- `INSERT` : 데이터를 테이블에 삽입
- `UPDATE` : 기존 데이터 수정
- `DELETE` : 데이터 삭제
- `SELECT` : 데이터 조회

```sql
INSERT INTO users (id, name, email) VALUES (1, 'Alice', 'alice@example.com');

UPDATE users SET email = 'alice_new@example.com' WHERE id = 1;

DELETE FROM users WHERE id = 1;

SELECT * FROM users WHERE name = 'Alice';
```

## **3. DCL (Data Control Language)**

**DCL**은 **사용자의 권한을 관리**하거나 **보안 설정**을 수행하는 데 사용한다.

- 데이터베이스 사용자에게 권한을 **부여하거나 취소**
- 보안을 유지하고 접근을 제어

### **명령어**

- `GRANT` : 특정 사용자에게 권한 부여
- `REVOKE` : 특정 사용자에게 부여된 권한 취소

```sql
GRANT SELECT, INSERT ON users TO 'username';

REVOKE INSERT ON users FROM 'username';
```

# **INNER JOIN, OUTER JOIN, CROSS JOIN**

SQL JOIN은 **두 개 이상의 테이블을 결합하여 관련 데이터를 조회**하는 데 사용되며, JOIN의 종류에 따라 데이터를 결합하는 방식이 달라진다.

## **1. INNER JOIN**

- 두 테이블에서 **공통된 키**를 기준으로 **일치하는 데이터만 반환**
- **일치하지 않는 데이터는 제외**

```sql
SELECT users.name, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id;
```

## 2. OUTER JOIN

- 두 테이블 간의 **일치 여부에 관계없이 특정 테이블의 모든 데이터**를 포함
- OUTER JOIN 세 가지
    - **`LEFT OUTER JOIN` :** 왼쪽 테이블의 모든 데이터 + 오른쪽 테이블에서 일치하는 데이터
    - **`RIGHT OUTER JOIN` :** 오른쪽 테이블의 모든 데이터 + 왼쪽 테이블에서 일치하는 데이터
    - **`FULL OUTER JOIN` :** 두 테이블의 모든 데이터 + 일치하지 않는 데이터 (Null로 표시)

## **3. CROSS JOIN**

- 두 테이블의 **카티션 곱(Cartesian Product)**를 반환
- 한 테이블의 모든 행과 다른 테이블의 모든 행을 조합한다.

```sql
SELECT users.name, products.product_name
FROM users
CROSS JOIN products;
```
