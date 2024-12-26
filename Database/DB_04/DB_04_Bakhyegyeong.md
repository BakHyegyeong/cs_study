# Select 문법

- `where` : 행의 제한(조건)
    
    → **where절은 그룹함수를 사용할 수 없다.**
    
- `group by` : 데이터를 그룹으로 묶을 때
- `having` : 그룹에 대한 조건
- `order by` : 정렬(내림 `desc`, 오름 `asc` → 오름이 기본)

**from → where → group by → having → select → order by** 순으로 실행된다.

## group by

`Group by [column 이름 | expr(표현식) | position]`

: 데이터를 **그룹**으로 묶을 때 특정 column에 대해 동일한 값을 가지는 행들을 **하나의 행으로 처리**

→ 통계 작업에 사용

```sql
SELECT userid,
	   SUM(price * amount) AS 'total price'
FROM buytbl
GROUP BY userid;
```

: Table에서 column에 대한 그룹(동일한 값을 가진 행)을 생성

- group by를 사용하면 **반드시 복수행 함수를 사용**해주어야 한다.
- **group by에 사용된 column과 복수행 함수에서 사용된 Column 외에는 select에서 사용될 수 없다.**

> ⭐복수행 함수
> : n개의 입력(여러 행의 결과)에 1개의 출력
> - `avg(column)` : 평균
> - `count(column or *)` : 행의 개수   
>    → null값을 제외하고 return함, ifnull을 사용하면 null을 제외하지 않고도 가능하다.    
> - `min(column) / max(column)` : 행의 최솟값과 최댓값   
>    → 이 뒤에 일반 column이 들어가면 잘못된 결과값이 나오게 된다.    
>- `sum()` : 총합


### having

**만들어진 그룹에 대한 조건**

→ `where`과 `having`을 많이 헷갈려하지만, **`where`은 Table에 대한 조건**을 의미한다. <br>
→ **`where` 절은 `group by`보다 먼저 실행**되기 때문에 만들어진 그룹에 대한 조건은 `having`을 사용해야 한다.

→ 조건에 사용되는 column은 group by에도 사용되는 column이어야 한다.

+) having은 집계 함수이 사용 가능하지만 where은 집계 함수 사용은 불가능하다.

**예시**

```java
-- 에러발생
SELECT userid AS `사용자 아이디`
		 ,SUM(price * amount) AS `총 구매액`
FROM buytbl
WHERE SUM(price * amount) > 100
GROUP BY userid; 

-- 옳은 정답
SELECT userid AS `사용자 아이디`
		 ,SUM(price * amount) AS `총 구매액`
FROM buytbl
GROUP BY userid
HAVING SUM(price * amount) > 100;
```

- 문법적 오류 : where 절에는 그룹 함수가 사용될 수 없다.
- 의미적 오류 : sum의 대상이 묘사되지 않았다.
    
    → 어떤 그룹의 sum인지 등
    

## order by

`order by [컬럼명 인덱스(select의 index) 별칭명] [desc | asc]`

→ ﻿order by는 column들을 열거해 여러 개를 사용할 수 있다.

- `limit n` : 표시할 행의 개수를 지정할 수 있음.
- 기본값 오름차순(ASC), 내림차순(DESC)

```sql
SELECT userid,
	   SUM(price * amount) AS 'total price'
FROM buytbl
GROUP BY userid
ORDER BY `total price` asc;

---
SELECT userid,
	   round(AVG(amount)) AS `amount avg`
FROM buytbl
GROUP BY userid
ORDER BY 2;
```

# 외래키의 참조무결성 옵션

외래키와 기본키간의 관계를 지키는 것으로 데이터의 정확성과 일관성을 보장한다.

- 외래 키의 일관성 : 외래 키의 값은 참조하는 테이블의 기본 키 값이거나 NULL 이어야 한다.
- 데이터 무결성 보장 : 저장된 데이터는 항상 유효하고 일관성 있게 유지된다.
- DELETE / UPDATE 연산 제한 : 부모 테이블에서 레코드를 삭제하거나 기본 키 값을 변경할 때 제약

## delete / update

- `on [delete | update] cascade` : 부모 데이터가 삭제/변경되면 자식 데이터도 함께 삭제/변경
    
    → 너무 위험하기에 사용X
    
- `on [delete | update] restrict`: 자식 테이블에 부모 테이블의 기본키 값이 없는 경우에만 부모 테이블에서 삭제할 수 있다.
- `on [delete | update] no action` : 부모 데이터가 삭제/변경될 때 참조 무결성을 위반하는 행위라면 취소시킨다.
- `on [delete | update] set null` : 부모 데이터 삭제 시 자식 데이터 값을 null 값으로 변경한다.
- `on [delete | update] set default` : 부모 데이터 삭제 시 자식 데이터 값을 default 값으로 변경한다.

## insert

- **automatic** : 부모 테이블에 기본키가 없는 경우 기본키를 생성한 후 자식 테이블에 입력한다.
- set null : 부모 테이블에 기본키가 없는 경우 자식 테이블의 외래키를 null 값으로 둔다.
- set default : 부모 테이블에 기본키가 없는 경우 자식 테이블의 외래키를 default 값으로 둔다.
- **dependent** : 부모 테이블에 기본키가 존재할 경우에만 자식 테이블에 입력을 허용한다.
- no action : 데이터를 입력할 때 참조 무결성을 위반하는 행위라면 취소시킨다.

# **DROP, TRUNCATE, DELETE의 차이점**

- `DROP` : 데이터베이스, 테이블을 삭제하는 역할
    
    → 테이블을 잘못 만들었거나, 더이상 필요 없는 경우 사용한다.
    
- `TRUNCATE` : 테이블을 초기화 시키는 역할
    
    → 테이블의 모든 행 일괄삭제
    
- `DELETE` : where절을 사용해 테이블에 있는 데이터를 하나하나 선택하여 제거하는 역할
    
    → where 절을 사용하지 않더라도 내부적으로는 한 줄씩 지우는 과정으로 이루어진다.
    

    ```sql
    DROP TABLE 테이블명;
    TRUNCATE TABLE 테이블명;
    DELETE FROM 테이블명;
    DELETE FROM 테이블명 WHERE 조건;
    ```

<br>

![](https://velog.velcdn.com/images/sally3921/post/15cde066-41cb-4565-aa44-4c0855801ac5/image.png)