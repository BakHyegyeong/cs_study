# JVM 메모리 영역의 구성
## JVM(자바 가상 머신)
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtclVx%2Fbtq4Xfml6Dy%2Fnzb5xxlGG1fr5iBGUMv77K%2Fimg.png" width="70%">

  - 자바로 만들어진 프로그램을 컴파일하여 만들어지는 바이트 코드를 OS에 맞게 해석하여 실행하는 역할을 한다.
  - 구성요소
    - 클래스 로더 : 클래스를 처음 참조할 때, 해당 클래스를 로드하고 연결하는 역할
    - 실행 엔진 : 클래스를 실행시키는 역할
      - 인터프리터 : 바이트 코드를 한 줄씩 읽어서 실행(느림)
      - JIT 컴파일러 : 인터프리터 방식으로 실행하다가 적절한 시점에 바이트 코드 전체를 컴파일하여 기계어로 변경 후 기계어로 직접 실행하는 방식
      - 가비지 콜렉터 : 동적으로 할당된 메모리 중 더 이상 사용되지 않는 메모리를 해제하는 프로세스
###### 컴파일 : 소스 코드를 바이트 코드로 변환하는 작업(.java -> .class)
###### 바이트 코드 : 컴퓨터가 인식할 수 있는 0과 1로 구성된 이진코드
## JVM 런타임 데이터 영역(Runtime Data Area) 

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcEjHLD%2Fbtq4YtqCAGY%2FrrVrI45UWSH2LqslkP8Wg0%2Fimg.png" width="70%">

#### 프로그램 수행을 위해 OS로부터 할당받은 메모리 공간이며, 크게 5가지 영역으로 나뉜다.
  - 프로그램 카운터 레지스터(PC Register) : 스레드마다 하나씩 존재하며, 스레드가 어떤 명령을 실행할지 가리키는 포인터로써, 현재 수행 중인 JVM 명령의 주소를 갖는다.
  - JVM 스택 영역(JVM Stack Area) : 임시로 할당되았다가 메소드를 빠져나가면 소멸되는 매개변수, 지역변수, 리턴 값 등을 저장하는 영역
  - 네이티브 메소드 스택(Native method stack) : 컴파일된 자바 바이트 코드가 아닌 C, C++처럼 기계어로 작성된 코드를 실행하는 영역
  - 힙 영역(Heap Area) : new 키워드로 생성된 객체와 배열을 저장하는 영역으로, 가비지 컬렉터가 수거 대상을 찾아 메모리를 확보한다.
    - Young 영역 : 새롭게 생성된 객체가 할당되는 영역
    - Old 영역 : GC로부터 오랫동안 살아남은 객체가 복사되는 영역
    - Permanent 영역 : JVM에서 클래스 메타데이터(클래스와 메소드, 필드 등의 정보)를 저장하는 곳
  - 메소드 영역(Method Area = Static Area) : 변수 타입, 접근 제어자, static 메서드 등과 같은 클래스와 관련된 정보를 저장하는 영역으로, static 변수, 인터페이스 등이 저장된다.
    - Runtime Constant Pool : static 영역에 존재하는 상수들을 저장하는 공간. 상수 풀은 힙에 위치하며, 메소드 영역에는 상수 풀의 주소값만 저장한다.
- 스레드가 공유하는 영역 : 메소드 영역, 힙 영역
- 스레드마다 독립적으로 가지는 영역 : JVM 스택 영역, 네이티브 메소드 스택 영역, 프로그램 카운터 레지스터

### JDK vs JRE vs JVM
- JDK(Java Development Kit) : 자바 프로그램을 개발하기 위한 도구 모음 (JRE + 개발 도구)
- JRE(Java Runtime Environment) : 자바 프로그램을 실행하기 위한 환경 (JVM + 라이브러리)
- JVM(Java Virtual Machine) : 자바 바이트 코드를 OS에 맞게 해석하여 실행하는 역할
