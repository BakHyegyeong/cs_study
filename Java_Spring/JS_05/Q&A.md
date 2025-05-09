## Q1. 접근 제어자를 사용하는 이유
- 프로젝트의 규모가 커진 상황에서 코드 관리에 용이하고 보안 측면에서 효율적이다.
- 클래스 내부에 선언된 데이터의 부적절한 사용을 막기 위해서이다.

아래와 같은 상황에서 주로 사용된다.

- 클래스와 인터페이스를 다른 패키지에서 사용하지 못하도록 막을 때
- 객체 생성을 막기 위해 생성자를 호출하지 못하게 할 때
- 필드나 메소드를 사용하지 못하도록 막아야 할 때

## Q2. 접근 제어자의 종류와 사용처
- public 접근 제한자 : 단어 뜻 그대로 외부 클래스가 자유롭게 사용할 수 있도록 한다.
- protected 접근 제한자 : 같은 패키지 또는 자식 클래스에서 사용할 수 있도록 한다.
- default 접근 제한 : 같은 패키지에 소속된 클래스에서만 사용할 수 있도록 합니다.
  → 다른 세 가지 접근 제한자가 적용되지 않으면 자동으로 default 접근 제한을 가진다.
- private 접근 제한자 : 단어 뜻 그대로 개인적인 것이라 외부에서 사용될 수 없도록 한다.

## Q3. 접근 제어자 사용 시 주의할 점
- 클래스 멤버 :public, protected, private, default
- 클래스 : public, default 만 쓰임
    - protected는 상속과 관련되있어서 생성자나 메서드 등에서 쓰임
    - private는 외부에서 접근이 불가능해 의미가 없음

## Q4. 접근 제어자를 사용함으로써 객체 지향 원칙 중 1가지를 만족시킬 수 있는데 무엇인가?
- 캡슐화
  - 실제 구현 부분을 외부에 드러내지 않는 접근 제어자/ 인터페이스를 의미한다.
    → 변수와 메서드를 하나로 묶고, 데이터를 외부에서 직접 접근하지 않고 함수를 통해서만 접근하는 것
    - 캡슐화에서는 정보은닉 개념이 중요하다.
      → 정보 은닉 : 다른 객체에서 자신의 정보를 숨기고 자신의 연산만을 통하여 접근을 허용하는 것 
  
  
    자바 윈칙 4가지
     - 캡슐화
     - 상속
     - 다형성
     - 추상화
    
    solid 설계 원칙 5가지
     - 단일 책임 원칙(SRP)
     - 개방 폐쇄 원칙(OCP)
     - 리스코프 치환 원칙(LSP)
     - 인터페이스 분리 원칙(ISP)
     - 의존 역전 원칙(DIP)

## Q5. 멤버의 접근 범위가 클래스의 접근 범위보다 넓을 수 없다는 것은 무슨 의미인가요?
클래스 내부의 멤버(필드, 메서드)는 해당 클래스의 접근 수준에 의존한다.
- 예를 들어, private 클래스 안의 public 메서드는 외부에서 접근이 불가능하다.
- 리스코프 치환 원칙 : 하위 타입은 상위 타입의 행위를 대체할 수 있어야 한다는 원칙을 따르기 위해 접근 범위를 제한

## Q6. 자바의 컴파일 과정은?
1. 작성: .java 파일 생성. 
2. 컴파일: javac로 바이트코드(.class)로 변환. 
3. 클래스 로드: JVM이 바이트코드를 메모리에 로드. 
4. 실행: JIT 또는 인터프리터가 바이트코드를 기계어로 변환해 JVM이 실행.

## Q7. 왜 바이트코드로 변환해야 할까요?
- 자바는 운영체제에 독립적이지만, 기계어는 플랫폼마다 다르기 때문에 이를 해결하기 위해 Javac 컴파일러가 자바 소스코드를 중간 언어인 바이트코드로 변환한다. 
- JVM이 이 바이트코드를 실행하며, 필요시 JIT 컴파일러가 기계어로 변환해 성능을 높인다. 따라서 바이트코드는 플랫폼 독립성을 제공하면서도 효율적으로 실행될 수 있다.

## Q8. 클래스로더는 바이트 코드를 JVM 메모리 어디에 올리는가?
- 메소드 영역 -> 클래스 관련 정보를 저장하는 영역

## Q9. JVM 구성 요소
- 클래스 로더
- 실행엔진
    - GC
    - 인터프리터
    - JIT 컴파일러
- 메모리
    - PC 레지스터
    - 스택
    - 네이티브 메서드 스택
    - 힙
    - 메서드 영역

## Q10. JVM이 자바 프로그램을 실행할 때 메모리 관리는 어떻게 이루어지나요?
1.    메서드 영역 : 클래스 정보(메타데이터), 정적 변수, 상수 풀
2.    힙 : 동적으로 생성된 객체와 배열.  모든 스레드가 공유
3.    스택 : 각 스레드마다 생성되며, 메서드 호출 시 지역 변수와 호출 스택을 저장
4.    PC 레지스터 : 각 스레드가 실행 중인 명령어의 주소를 저장
5.    네이티브 메서드 스택 : C/C++ 등 네이티브 코드 실행에 필요한 데이터
