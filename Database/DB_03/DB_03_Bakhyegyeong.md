# **SQL의 DDL, DML, DCL의 정의와 역할**

| 언어 종류 | 정의 | 예시 |
| --- | --- | --- |
| Data Definition Language (DDL) | 데이터 정의어 <br> : 테이블의 구조를 정의 | CREATE/ALTER/DROP/RENAME |
| Data Manipulation Language (DML) | 데이터 조작어 <br>  : 테이블의 데이터를 조작 | SELECT/INSERT/UPDATE/DELETES |
| Data Control Language (DCL) | 데이터 제어어 <br>  : 권한 제어 | GRANT(권한 부여)/REVOKE(권한 회수) |
| Transaction Control Language (TCL) | 트랜잭션 제어어 <br>  : DML의 결과를 작업단위별로 제어 | COMMIT/ROLLBACK |


# INNER JOIN, OUTER JOIN, CROSS JOIN의 차이점

## 조인

n개 이상의 Table을 서로 **묶어서 하나의 결과집합**을 만들어 내는 것

- Table의 관계 : 1:1 / 1:N / M:N  → 식별관계, 비식별관계를 통해 1:N으로 풀기
- JOIN 조건
    - ON : **연산자**를 통한 조건을 명시
        - EQUI JOIN : = 연산자
        - NON EQUI JOIN : =를 제외한 나머지 연산자, <=
            
            → 하나의 행이 여러 개의 행을 가질 수 있다.
            
            → 따라서 ORDER BY와 집계함수를 사용할 때 기준을 잘 정해야한다. 
            
    - USING : **부모의 기본키와 자식의 외래키가 동일할 때 사용**

## 조인의 종류

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvjWa2%2FbtqXb1HydaK%2FKCAmZlQx1uMDOFjxlRvrbk%2Fimg.png)

## Inner 조인

: JOIN 조건을 만족하는 데이터만 출력한다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcTNRKJ%2FbtqW8GRdK8Z%2FVgIX8mSmqKprecf7EZs8dK%2Fimg.png)

```sql
--2개 테이블 inner join
select a.customer_id, a.first_name, a.last_name, a.email, 
b.amount, b.payment_date --선택할 column
from customer a
inner join payment b -- a와 b를 merge
on a.customer_id = b.customer_id  -- customer_id가 같은 row끼리
where a.customer_id = 2 ;  -- customer_id가 2인 row만 조회하기
```

```sql
--3개 테이블 inner join 
select a.customer_id, a.first_name, a.last_name, a.email, 
b.amount, b.payment_date, -- 선택할 column은 맨 마지막에 선택하자
c.first_name as s_first_name, c.last_name as s_last_name 
from customer a
inner join payment b on a.customer_id = b.customer_id 
inner join staff c on b.staff_id = c.staff_id 
;
```

## Outer 조인 (LEFT, RIGHT, FULL)

: JOIN 조건을 만족하지 않은 데이터도 출력한다. 

| JOIN 종류 | 설명 |
| --- | --- |
| LEFT | JOIN키워드의 **왼쪽**에 존재하는 TABLE을 모두 출력 |
| RIGHT | JOIN키워드의 **오른쪽**에 존재하는 TABLE을 모두 출력 |
| FULL | LEFT, RIGHT JOIN을 합쳐둔 것 |

### Left Outer 조인

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FBeIsb%2FbtqXb09H7tU%2FRnMdFg8Fk5sGPoiAz0Jm70%2Fimg.png)

```sql
--left, right outer join
select a.id as id_a, 
a.fruit as fruit_a,
b.id as id_b,
b.fruit as fruit_b
from basket_a a
left join basket_b b -- a를 기준으로 b를 합친다. a는 다 출력한다.
-- 만약에 a에 해당하는 b가 없으면 b는 NULL값으로 출력함
on a.fruit = b.fruit;
```

### Left Only Outer 조인

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbknWxc%2FbtqXb1AMma0%2FUEgYrvMGeAyQdKoZwhvjEk%2Fimg.png)

```sql
where b.id is null; 
```

위의 조건을 넣어주면 only 조인으로 왼쪽에만 있는 데이터를 출력할 수 있다.

### Full Outer 조인

Inner, Left Outer, Right Outer 조인 집합을 모두 출력하는 조인 방식

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FXyhjY%2FbtqW8GcBbdl%2F9C8Bi2HequVC6V0CQctYxk%2Fimg.png)

```sql
-- Full outer join
select e.employee_name, d.department_name from e 
full outer join d 
on d.department_id = e.department_id;
```

## 각 조인의 결과물

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdE3sa8%2FbtqWVSsbnZs%2F61KWDykeT9FaYALZFJ8LMK%2Fimg.png)

**: FULL OUTER JOIN은 위의 행을 모두 출력한다.**

### Full Only Outer 조인

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdhlTDp%2FbtqXb2zGJgj%2FbNDtYBhDRgIsBfnI0MNN4k%2Fimg.png)

```sql
select e.employee_name, d.department_name from e 
full outer join d on d.department_id = e.department_id
where e.employee_name is null or d.department_name is null; --only join
```

## Cross 조인

: JOIN조건을 주지 않고 1:N관계를 전부 매핑하는 것

→ 각 Table에서 생길 수 있는 모든 경우의 수를 표현 <br>
→ (Table 행 개수 * Table 행 개수)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc6ThPq%2FbtqW4hjTPLg%2FQ226MW4kM8KMo08XqziYw0%2Fimg.png)

