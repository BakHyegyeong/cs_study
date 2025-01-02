## 정규화 과정

: **데이터의 중복을 방지하고 보다 효율적으로 데이터를 저장**함으로써 속성들을 본래의 Table에 정확히 위치시키기 위해서 하는 것. (Null값 제외)

→ 논리적으로 **작은 단위의 Table로 분리**되어진다.

**장점**

- 이상 현상(Anomaly)을 제거할 수 있다.
- 새로운 데이터 형의 추가로 인한 확장 시, 그 구조를 변경하지 않아도 되거나 일부만 변경해도 된다.

**단점**

- 릴레이션의 분해로 인해 릴레이션 간의 JOIN연산이 많아진다.
    
    → 조인이 많이 발생하여 성능 저하가 나타나면 **반정규화(De-normalization)**를 적용할 수도 있다.
    

### 제 1정규형

: 모든 도메인이 더 이상 분해될 수 없는 원자 값만으로 구성되어야 하는 것

<p align="center">
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbNbQUm%2FbtqT18yag04%2FpTXJX3wB23ouk8az7EgWQ1%2Fimg.png" alt="Image 1" height="200">
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbMlNZj%2FbtqT17FWVot%2FjUKTAUyOdrH83pRraKw3K0%2Fimg.png" alt="Image 2" height="200">
</p>


### 제 2정규형

: 제 1정규형을 만족하며 기본키가 아닌 **모든 속성이 기본키에 완전 함수 종속적**인 형태

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FylbaZ%2FbtqT8Jc4K3s%2F0VFTPoKKFkbxZghKWDwKo1%2Fimg.png)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbluCnc%2FbtqT7VEOf04%2FMe8DfY7rtycgJPYlYQKEWK%2Fimg.png)

### 제 3 정규형

: 제 2 정규형을 만족하며 기본키가 아닌 **모든 속성이 이행적 함수 종속을 만족하지 않는** 상태

→ 제 3 정규형은 분해될 때 **같은 속성을 가지면서 분해**된다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FenwN1N%2FbtqUeiMyErd%2FsP8NKCe70NKsZncGuhO9uK%2Fimg.png)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fci1le3%2FbtqUeXnPnpD%2FyKkURqr8cZl21f5erx42QK%2Fimg.png)

### 보이스/코드 정규형

: **후보키가 아닌 결정자가 가진 함수 종속 관계를 제거**하는 것

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbBN6xu%2FbtqT6IlqRF4%2FMvBoxYMxtgS1JT7t1AymnK%2Fimg.png)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3cbHr%2Fbtq3mNylPan%2Fc6b2lBuH4OkdDNmrzGHWUk%2Fimg.png)

: **학번과 과목이 복합키 + 교수는 한 과목만 가르친다고 했을 때** **교수 속성은 결정자**이다.
→ 브라운 - 데이터베이스 / 블랙 - 데이터베이스 로 교수만 알면 과목을 알 수 있기 때문이다.
→ 결정자임에도 불구하고 **교수 속성은 후보키가 아니기 때문**에 이 교수 속성에 종속되는 과목 속성을 분리해야 하는 것이다.

[추가 설명](https://komas.tistory.com/57)


# 역정규화

데이터베이스의 성능 향상을 위하여, **데이터 중복을 허용하고 조인을 줄이는** 데이터베이스 성능 향상 방법

→ **조회(select) 속도를 향상**시키지만, 데이터 **모델의 유연성은 낮아**진다.

## 필요성

- 정규화에 충실하여 종속성, 활용성은 향상 되었지만 수행속도가 느려진 경우
- 다량의 범위를 자주 처리해야하는 경우
- 특정 범위의 데이터만 자주 처리하는 경우
- 요약/집계 정보가 자주 요구되는 경우

## 과정

| **단계** | **내용** |
| --- | --- |
| 1. 반정규화 대상 조사 | - 범위 처리 빈도수 조사 |
|  | - 대량의 범위 처리 조사 |
|  | - 통계성 프로세스 조사 |
|  | - 테이블 조인 개수 |
| 2. 다른 방법 유도 검토 | - 뷰(VIEW) 테이블 활용 가능성 검토 |
|  | - 클러스터링 적용 가능성 분석 |
|  | - 인덱스의 조정 |
|  | - 응용 애플리케이션 최적화 검토 |
| 3. 반정규화 적용 | - 테이블 반정규화 작업 |
|  | - 속성의 반정규화 적용 |
|  | - 관계의 반정규화 수행 |

## 반정규화 적용 기법

- 계산된 컬럼 추가 : 총판매액, 평균잔고 등과 기존 속성에서 가공처리를 통해 생성되는 속성을 추가한다.
- 테이블 수직 분할하나의 테이블을 컬럼 단위로 분할하여 새로운 테이블로 재구성함.
- 테이블 수평분할하나의 테이블에 포함된 행을 기준으로 테이블을 분할함.
- 테이블 병합1:1 또는 1:n 관계의 테이블을 병합하여 하나로 재구성한다.

[반정규화 적용 기법 상세](https://velog.io/@dddooo9/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EB%B0%98%EC%A0%95%EA%B7%9C%ED%99%94)

