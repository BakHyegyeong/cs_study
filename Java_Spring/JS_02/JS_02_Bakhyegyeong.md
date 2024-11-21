# JVM (Java **V**irtual **M**achine)

: 자바를 실행하기 위한 가상 기계(컴퓨터)

→ OS에 종속받지 않고 CPU가 Java를 인식, 실행할 수 있게 하는 가상 컴퓨터이다.

## 자바의 특징

### 1. OS에 independence하다.

**: OS 위에 JVM(Java Virtual Machine)을 깔아서 의존하지 않게 한다.** 즉 어떤 운영체제이든지간에 사용할 수 있다.

- JDK(Java Development Kit) : 소프트웨어로써 운영체제에 설치하면 JRE를 사용할 수 있다.
- JRE(Java Runtime Environment): JVM과 API를 포함
  
![](https://velog.velcdn.com/images/calla3494qhk/post/1fc850c3-dc91-4113-a3c4-78f2318223be/image.png)

### 2. compile 언어이다.

: Java 소스코드, 즉 **원시코드(`*.java`)는 CPU가 인식을 하지 못하므로 기계어로 컴파일**을 해줘야한다.
→ Java는 이 JVM이라는 가상머신을 거쳐서 OS에 도달하기 때문에 OS가 인식할 수 있는 기계어로 바로 컴파일 되는게 아니라 **JVM이 인식할 수 있는 Java bytecode(`*.class`)로 변환**된다.

- 클래스를 하나의 파일(`a.java`)로 생성한다.
- compile 을 통해 `a.class`(**byte code** = 실행파일)로 변환한다.
- `a.class`파일은 JVM이 이해할 수 있는 언어 (이진수)로 이루어져있다.
    
    → JVM이 OS가 bytecode를 이해할 수 있도록 해석해주는 것이다.
    
- `a.class`파일을 interpriter 또는 JIT 컴파일러를 통해 output을 볼 수 있다.

    
![](https://velog.velcdn.com/images/calla3494qhk/post/2ed81d90-b094-4a53-961e-148e87fda0fb/image.png)

- `javac` 명령어를 통해 직접 class파일을 생성할 수 있다

## JVM 구성 요소

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtclVx%2Fbtq4Xfml6Dy%2Fnzb5xxlGG1fr5iBGUMv77K%2Fimg.png)

- 클래스 로더(Class Loader)
- 실행 엔진(Execution Engine)
    - 인터프리터(Interpreter)
    - JIT 컴파일러(Just-in-Time)
    - 가비지 콜렉터(Garbage collector)
- 런타임 데이터 영역 (Runtime Data Area)

### 클래스 로더

: JVM 내로 클래스 파일(`*.class`)을 로드하고, 링크를 통해 배치하는 작업을 수행하는 모듈
→ 클래스를 처음으로 참조할 때, **해당 클래스를 로드하고 링크한는 역할**을 한다.

### 실행 엔진

: 클래스를 실행시키는 역할

→ 클래스 로더가 JVM내의 런타임 데이터 영역에 바이트 코드를 배치시키고, 이것은 **실행 엔진에 의해 실행**

→ 바이트 코드를 실제로 **JVM 내부에서 기계가 실행할 수 있는 형태로 변경**

- 인터프리터 : 실행 엔진은 자바 바이트 코드를 명령어 단위로 읽어서 실행
    
    → 한 줄씩 수행하기 때문에 느리다는 단점이 존재한다.
    
- JIT : 인터프리터 방식으로 실행하다가 적절한 시점(자주 사용되는 코드)에 바이트 코드 전체를 컴파일하여 기계어로 변경 후 기계어로 직접 실행하는 방식 <br>
    → **변환된 기계어를 캐싱**할 수 있다. 이 덕분에 한번 변환된 기계어는 다시 변환할 필요가 없어 효율적이다.
- 가비지 콜렉터 : 더 이상 사용되지 않는 인스턴스를 찾아 메모리에서 삭제한다.

## JVM Runtime Data Area

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
