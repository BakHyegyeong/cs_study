# 가비지 컬렉션

: 메모리 관리 기술 중 하나로 시스템에서 더이상 사용하지 않는 동적 할당된 메모리 블럭을 찾아 **자동으로 다시 사용 가능한 자원으로 회수**하는 **프로세스**

→ Java에서 명시적으로 불필요한 데이터를 표현하기 위해서 일반적으로 null을 선언해준다.

> 💡 가비지와 가비지 컬렉터 <br> - 가비지 (Garbage) : 유효하지 않은 메모리 <br>- 가비지 컬렉터 : 메모리 관리를 담당하는 시스템에서 가비지 컬렉션을 수행하는 구성 요소


## 사용하지 않는 객체의 메모리를 청소해야 하는 이유

**사용하지 않는 객체의 메모리 점유는 결국 메모리 누수**로 이어진다.

**→ 메모리는 한정된 자원**이기 때문에 사용하지 않거나 필요가 없는 부분은 해제를 해주는 것이 맞다.

C와 C++ 같은 Unmanaged language는 free()와 같은 함수를 사용해서 메모리를 직접 메모리를 해제해야 하지만 JAVA에서는 GC(Garbage Collector)가 대신 해주고 있다.

## 가비지를 판단하는 기준 - **Reachability**

- **Reachable :** 어떤 객체에 유효한 참조가 존재
- **unreachable :**  어떤 객체에 유효한 참조가 존재하지 않음
    
    → 가비지 컬렉터의 정리 대상
    

## JVM의 Heap 메모리 영역

JVM의 메모리는 크게 **클래스 영역, 자바 스택, 힙, 네이티브 메소드 스택**으로 4개의 영역으로 나뉜다.

가비지 컬렉터에서는 **힙 메모리**를 다루게 되며 힙 메모리는 아래와 같은 전제로 설계되었다.

- 대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
- 오래된 객체에서 새로운 객체로의 참조는 아주 적게 존재한다.

→ **객체는 대부분 일회성되며, 메모리에 오랫동안 남아있는 경우는 매우 드물다.**

→ **객체의 생존 기간**에 따라 Heap 영역을 **Young, Old, Perm** 총 3가지로 나누었다.

## JVM의 Heap 메모리 영역의 분류

![](https://velog.velcdn.com/images/kyungwoon/post/4930b77a-b579-40e1-bd49-155e0a325213/image.png)

1. Young Generation(Young 영역)이란?
    - 새롭게 생성된 객체가 할당(Allocation)되는 영역
    - 대부분 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라진다.
    - Young 영역에 대한 가비지 컬렉션(Garbage Collection)을 **Minor GC**라고 부른다.
2. Old Generation(Old 영역)이란?
    - Young 영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
    - Young 영역보다 크게 할당되며, 영역의 크기가 큰 만큼 가비지는 적게 발생한다.
    - Old 영역에 대한 가비지 컬렉션을 **Major GC 또는 Full GC**라고 부른다.
3. Permanent Generation(Perm 영역)이란?
    - JVM에서 클래스 메타데이터(클래스와 메소드, 필드 등의 정보)를 저장하는 곳
    - 클래스 로더는 클래스 파일을 읽어들여서 이 영역에 클래스 메타데이터를 저장한다.
    - 이 정보들은 JVM 실행 도중에 변경되지 않으며, JVM 종료 시까지 유지된다.

### Old 영역이 Young 영역보다 크게 할당되는 이유

Young 영역의 수명이 짧은 객체들은 큰 공간을 필요로 하지 않으며 큰 객체들은 Young 영역이 아니라 바로 Old 영역에 할당되기 때문이다.

### 카드 테이블

- 배경
    
    : 예외적으로 **Old 영역에 있는 객체가 Young 영역의 객체를 참조**하는 경우가 존재한다.
    
    → 이를 대비하기 위해 Old 영역에는 512 bytes의 덩어리(Chunk)로 되어 있는 카드 테이블(Card Table)이 존재한다.
    

- 카드 테이블 : Old 영역에 있는 객체가 Young 영역의 객체를 참조할 때 마다 그에 대한 정보를 표시
    
    → Young 영역에서 가비지 컬렉션(Minor GC)가 실행될 때 모든 Old 영역에 존재하는 객체를 검사하여 **참조되지 않는 Young 영역의 객체를 식별하는 것이 비효율적**이기 때문
    
    → Young 영역에서 가비지 컬렉션이 진행될 때 카드 테이블만 조회하여 GC의 대상인지 식별할 수 있도록 하고 있다.
    

## 가비지 컬렉션의 동작 방식

Young 영역과 Old 영역은 서로 다른 메모리 구조로 되어 있기 때문에, 세부적인 동작 방식은 다르다. 하지만 기본적으로 가비지 컬렉션이 실행된다고 하면 다음의 2가지 공통적인 단계를 따르게 된다.

### Stop The World

: GC를 실행하기 위해 JVM 애플리케이션 실행을 멈추는 것

→ GC를 실행하는 쓰레드를 제외한 **모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개**된다.

→ GC 튜닝은 이런 stop the world 시간을 줄이는 것을 의미한다.

> 💡 **Stop The World가 발생하는 이유** <br>
: GC가 실행되는 동안에는 모든 객체의 참조 관계를 추적하고, 
유효한 객체들과 그렇지 않은 객체들을 식별하여 메모리를 회수하기 때문이다.

### Mark and Sweep

- Mark : 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
- Sweep : Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업

Stop The World를 통해 모든 작업을 중단시키면, GC는 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 각각이 어떤 객체를 참고하고 있는지를 탐색하게 된다. 

그리고 사용되고 있는 메모리를 식별하는데, 이러한 과정을 Mark라고 한다. 이후에 Mark가 되지 않은 객체들을 메모리에서 제거하는데, 이러한 과정을 Sweep라고 한다.

## Minor GC - Young 영역에서의 GC

Young 영역은 1개의 Eden 영역과 2개의 Survivor 영역으로 이루어져 있다.

- Eden 영역: 새로 생성된 객체가 할당(Allocation)되는 영역
- Survivor 영역: 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역

### 동작 과정

1. 새로 생성된 객체가 Eden 영역에 할당된다.
2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되고 Minor GC가 실행된다.
    1. Eden 영역에서 사용되지 않는 객체의 메모리가 해제된다.
    2. Eden 영역에서 살아남은 객체는 1개의 Survivor 영역으로 이동된다.
3. 1~2번의 과정이 반복되다가 Survivor 영역이 가득 차게 되면 Survivor 영역의 살아남은 객체를 다른 Survivor 영역으로 이동시킨다.
    
    → 1개의 Survivor 영역은 반드시 빈 상태가 된다.
    
4. 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동(Promotion)된다.

![](https://velog.velcdn.com/images/yarogono/post/cb487aa7-f760-4af4-b13a-949f628aa426/image.png)

여기서 객체의 생존 횟수를 카운트하기 위해 Minor GC에서 객체가 살아남은 횟수를 의미하는 Age를 Object Header에 기록한다.

Minor GC 때 Object Header에 기록된 age를 보고 **Promotion 여부를 결정**한다.

## Major GC - Old 영역에서의 GC

Young 영역에서 오래 살아남은 객체는 Old 영역으로 Promotion 된다.

→ Major GC는 객체들이 계속 Promotion 되어 Old 영역의 메모리가 부족해지면 발생하게 된다.

**Young 영역**은 일반적으로 Old 영역보다 크키가 작기 때문에 GC가 보통 0.5초에서 1초 사이에 끝난다. 그렇기 때문에 **Minor GC는 애플리케이션에 크게 영향을 주지 않는다.** 

하지만 **Old 영역**은 Young 영역보다 크며 Young 영역을 참조할 수도 있다. 그렇기 때문에 **Major GC는 일반적으로 Minor GC보다 시간이 오래걸리며, 10배 이상의 시간을 사용**한다. 

참고로 Young 영역과 Old 영역을 동시하 처리하는 GC는 Full GC라고 한다.

### 동작 과정

객체를 메모리에서 삭제하는 대신 **객체들을 메모리의 한쪽으로 몰아서 메모리 공간을 최적화**하는 `compact`작업

→ 메모리 단편화를 최소화하고 성능을 향상

![](https://velog.velcdn.com/images/yarogono/post/d7ebdf41-8408-4b25-bba2-5dacb2ea8a3a/image.png)

## 가비지 컬렉션의 차이점

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdM4wqf%2FbtqUWs2lW8H%2FGvRECmsUIfZ2jhDoKhSCD0%2Fimg.png)

---

# StringBuilder, StringBuffer의 차이

- 공통점 : 문자열을 연산(추가, 변경)할 때 주로 사용하는 자료형
- 차이점 : 동기화 유무
    - StringBuilder : 동기화를 지원하지 않는다.
        
        → 각 메서드별로 Synchronized Keyword가 존재해 **멀티 스레드 환경에서도 사용**할 수 있다.
        
    - StringBuffer : 동기화를 지원한다.
        
        → **단일 스레드 환경**에서 사용한다.
        

### 정리

- StringBuffer : 문자열 연산이 많고 **멀티쓰레드 환경**일 경우
- StringBuilder : 문자열 연산이 많고 **단일쓰레드이거나 동기화를 고려하지 않아도 되는 경우**

---

## String과 StringBuilder, StringBuffer의 비교

### 불변/가변

- String : 불변
    
    → String 객체의 값은 변경할 수 없다.
    
    ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcmPEgz%2FbtsgYzzn6t2%2For71On05xe18UWA7gvJKC1%2Fimg.png)
    
    : 위의 사진처럼 String의 연산은 객체 자체의 값을 업데이트시키는 것이 아닌 **업데이트된 값을 저장한 영역을 따로 만들고 다시 변수가 그 영역을 다시 참조하는 방식**으로 이루어진다.
    
- StringBuilder, StringBuffer : 가변
    
    → 객체의 공간이 부족해지는 경우 **버퍼의 크기를 유연하게 늘릴 수 있다.**
    
    ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeVviG0%2Fbtsg0mNkhJo%2FfMRAQM4HfBcd0yen1zPK4K%2Fimg.png)
    
    : 내부 Buffer에 문자열을 저장해두고 그 안에서 추가, 수정, 삭제 작업을 수행하도록 설계되어 있다.
    

### 값 비교

- String : `equals()`
- StringBuffer / StringBuilder : `toString()` 으로 StringBuffer/StringBuilder 객체를 String 객체로 변환하고 난 뒤에 `equals()` 로 비교

### 정리

**문자열을 자주 읽어들이는 경우** 더 가벼운 **String**을 사용하면 더 성능이 좋다.

반면 문자열 **추가,수정,삭제 등의 연산**이 빈번하게 발생하는 경우 **StringBuilder와 StringBuffer**를 사용하는게 더 좋다.
