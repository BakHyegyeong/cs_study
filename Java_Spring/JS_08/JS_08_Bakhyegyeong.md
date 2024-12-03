# JVM Runtime Data Area

: 프로그램을 수행하기 위해 **OS에서 할당받은 메모리 공간**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcEjHLD%2Fbtq4YtqCAGY%2FrrVrI45UWSH2LqslkP8Wg0%2Fimg.png)

- **Method Area(메소드 영역) :** 클래스 변수의 이름, 타입, 접근 제어자 등과 같은 클래스와 관련된 정보를 저장한다. 그 외에도 static 변수, 인터페이스 등이 저장된다.
- **Heap Area(힙 영역) :** new를 통해 생성된 객체와 배열의 인스턴스를 저장하는 곳이다. 가비지 컬렉터는 힙 영역을 청소하며 메모리를 확보한다.
    - Young : 새롭게 생성된 객체가 할당(Allocation)되는 영역
    - Old : Young 영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
    - Permanent : JVM에서 클래스 메타데이터(클래스와 메소드, 필드 등의 정보)를 저장하는 곳
- **Stack Area(스택 영역) :** 메소드가 실행되면 스택 영역에 메소드에 대한 영역이 1개 생긴다. 이 영역에 메소드 안에서 사용되는 값들인 지역변수, 매개변수, 리턴값 등이 저장된다.
    
    → 프로그램 실행과정에서 임시로 할당되었다가 메소드를 빠져나가면 바로 소멸되는 특성의 데이터를 저장하기 위한 영역이다.
    
- **PC register(PC 레지스터) :** 현재 스레드가 실행되는 부분의 주소와 명령을 저장한다. (CPU의 레지스터와 다르다.)
    
    → Thread가 시작될 때 생성되며 생성될 때마다 생성되는 공간으로, 스레드마다 하나씩 존재한다.
    
- **Native Method Stack(네이티브 메소드 스택) :** 자바 외의 언어(C, C++ 등)로 작성된 코드를 위한 메모리 영역이다. JNI를 통해 사용된다.

# JVM 런타임 메모리 영역과 프로세스의 메모리 영역

![](https://cdn.inflearn.com/public/files/posts/0e107917-7450-4a98-8fff-a09a2a9475d6/02-javaheap720x318.jpg)

결론만 내자면 **프로세스의 힙 메모리 영역 안에 JVM 런타임 메모리 영역이 존재**한다. <br>
JVM 자체는 C/C++ 프로그램이기 때문에 힙을 제외한 다른 모든 메모리 영역은 C 프로그램에서와 동일하게 작동한다.

[프로세스 메모리영역과 JVM 메모리영역의 상관관계](https://www.inflearn.com/community/questions/1095377/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%A9%94%EB%AA%A8%EB%A6%AC%EC%98%81%EC%97%AD%EA%B3%BC-jvm-%EB%A9%94%EB%AA%A8%EB%A6%AC%EC%98%81%EC%97%AD%EC%9D%98-%EC%83%81%EA%B4%80%EA%B4%80%EA%B3%84?srsltid=AfmBOoq1smL2SVl8epeZp5i-Dzc8q5fPQjl-7AuNNXtjUYywJBVJEpgp)

[JVM의 메모리 구성요소와 영역에 관한 고찰](https://dkswnkk.tistory.com/440)

---

# OutOfMemoryError

: **JVM의 메모리가 부족하여 발생한 에러**

OutOfMemoryError가 발생하면 JVM은 **최대한 빨리 힙메모리를 확보하기 위해 Full GC(=Major GC)를 수행**한다. 

Full GC를 수행할 때는 STW(Stop The World)라는 현상이 발생하는데, **WAS의 모든 프로그램 수행을 멈추고 GC만 수행**한다. Full GC는 최대한 많은 자원을 사용하기 때문에 **WAS가 실행되는 서버의 CPU 사용률도 급격히 치솟는데**, 최악의 경우 100%가 계속 유지되는 경우도 많다.

Full GC가 몇 초 단위로 계속 반복되고 CPU 사용률도 100%로 계속 유지된다는 것은, **힙메모리에 Full GC를 해도 회수되는 메모리의 양이 적어서 다시 Full GC를 수행**하고, 그 후에도 같은 상황이 반복된다는 의미이다. 

## 발생 원인

자바 프로그램이 실행하는 동안에는 변수 등을 생성하면서 힙메모리의 영역을 요청해서 받았다가, 사용이 끝나면 풀어주는 일을 반복한다.

**→** 이 과정에서 **힙 메모리를 모두 사용해서 더 이상 메모리를 받을 수 없을 때** 발생한다.

### 메모리 누수

프로그램 내에 메모리 누수(Memory Leak)이 있어서 **메모리를 반납하지 않고 계속 새로운 메모리를 얻기만 하는 경우**

→ 보통 Collection 타입의 객체 내에서 **저장한 데이터를 지우지 않고 지속적으로 추가하는 경우에 발생**한다.

→ 많은 데이터를 저장하는 Collection에서는 **필요한 데이터만 남기고 필요없는 데이터는 지우도록 프로그램을 수정**함으로써 해결할 수 있다.

### 힙 메모리의 과도한 사용

프로그램의 계획을 잘못 세워서 **짧은 시간에 아주 많은 힙 메모리를 사용**하게 되거나 **힙 메모리의 크기가 작은 경우**

→ Dump파일 분석을 통해 많은 메모리를 사용하는 **로직을 수정**하거나 -Xmx 옵션을 사용해 **힙 메모리의 크기를 증가**시키는 방법으로 해결할 수 있다.

→ 후자의 방법은 GC Time의 증가를 동반하기 때문에 사전 테스트가 필요하다.

### 예외 별 원인

- **Java.lang.OutOfMemoryError: Java heap space :** Java의 Heap Memory 공간이 부족하여 발생
- **Java.lang.OutOfMemoryError : PermGen space :** JVM 기동시 로딩되는 Class 또는 String의 수가 너무 많을 경우 발생
- **Java.lang.OutOfMemoryError : Requested array size exceeds VM limit** : 사용할 배열의 사이즈가 VM에서 정의될 사이즈를 초과할 때 발생
- **Java.lang.OutOfMemoryError : request bytes for . Out of swap space?** : Java는 런타임시 물리적 메모리를 초과한 경우 가상메모리를 확장해 사용하는데 가용한 가상메모리가 없을 경우 발생
- **Java.lang.OutOfMemoryError : (Native method)** : JVM에 설정된 것보다 큰 native 메모리가 호출될 때 발생

## 원인 분석

### JVM Option

**OS/하드웨어별로 JVM의 설정을 변경하여 성능을 보장**하기 위한 것

- Standard Option : JVM 표준 옵션으로 벤더(JVM을 만드는 회사)와 상관없이 동일한 옵션을 가진다.
- Non-Standard Option : JVM표준이 아니라 Java의 버전, 벤더에 따라 추가되거나 없어 지는 옵션
    
    → 성능에 직접적으로 영향을 주는 옵션으로 `-X`, `-XX`로 시작한다.
    

### Dump 파일 분석

- Dump 파일 : 애플리케이션 운영 중 장애 혹은 성능 상 문제가 발생했을 때 애플리케이션의 상태를 스냅샷 형태로 파일로 저장한 것

이 Dump 파일을 분석해 **장애 발생 시 애플리케이션이 어떤 상태였는지, 무엇이 원인이였는지를 확인**할 수 있다.

### VisualVM
: JVM 메모리 모니터링 도구

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FXBy0M%2FbtrZPTmW7QK%2FqalmiHuH6Y5vV6nTweOIck%2Fimg.png)

---

# 가비지 컬렉션의 동작 방식

Young 영역과 Old 영역은 서로 다른 메모리 구조로 되어 있기 때문에, 세부적인 동작 방식은 다르다. 하지만 기본적으로 가비지 컬렉션이 실행된다고 하면 다음의 2가지 공통적인 단계를 따르게 된다.

### Stop The World

: GC를 실행하기 위해 JVM 애플리케이션 실행을 멈추는 것

→ GC를 실행하는 쓰레드를 제외한 **모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개**된다.

→ GC 튜닝은 이런 stop the world 시간을 줄이는 것을 의미한다.

> 💡 **Stop The World가 발생하는 이유** : GC가 실행되는 동안에는 모든 객체의 참조 관계를 추적하고, 
유효한 객체들과 그렇지 않은 객체들을 식별하여 메모리를 회수하기 때문이다.


### Mark and Sweep

- Mark : 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
- Sweep : Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업

Stop The World를 통해 모든 작업을 중단시키면, GC는 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 각각이 어떤 객체를 참고하고 있는지를 탐색하게 된다. 

그리고 사용되고 있는 메모리를 식별하는데, 이러한 과정을 Mark라고 한다. 이후에 Mark가 되지 않은 객체들을 메모리에서 제거하는데, 이러한 과정을 Sweep라고 한다.