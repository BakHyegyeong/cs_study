# 이펙티브 자바
## 2장 객체 생성과 파괴
### 아이템 1 생성자 대신 정적 팩터리 메서드를 고려하라
- 장점
  - 생성자와 달리 정적 팩터리 메서드는 이름을 가질 수 있다.
    - from : member엔티티 받아서 dto로 변환
    - of : registerForm을 받아서 member로 변환
  - 생성자와 달리 호출할 때마다 새로운 객체를 생성할 필요가 없다. 
    - new 키워드를 사용하지 않고 바로 객체를 생성할 수 있다.
  - 매번 다른 클래스의 객체를 반환할 수 있다. 
    - 생성자와 달리 부모 클래스르 받아서 자식 클래스 객체를 반환할 수 있다.
    - member 객체를 받아, admin, user, guest 객체를 반환할 수 있다.
- 단점
  - 정적 팩터리 메서드만 있는 클래스를 만들면, 생성자가 없어, 상속받는 하위 클래스를 만들 수 없다.

### 아이템 2 생성자에 매개변수가 많다면 빌더를 고려하라
- 생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면, 빌더 패턴을 사용하는 것이 더 낫다.
- 구현 방법
  - 매개변수가 4개, 5개, 6개인 생성자를 각각 만든다.(여러 개의 생성자를 만들어야 한다.)
  - 객체를 생성한 후 각 매개변수마다 setter 메서드를 호출하여 값을 설정한다.
  - 빌더 객체를 생성하여 매개변수를 설정하고, build 메서드를 호출하여 객체를 생성한다.
    - 장점
      - 매개변수의 순서를 신경쓰지 않아도 된다.
      - 매개변수가 많아도 가독성이 좋다.
    - 단점
      - 빌더 객체를 생성해야 하므로, 비용이 든다.
      - 적은 매개변수의 경우, 생성자를 사용하는 것보다 코드가 길어진다.

### 아이템 3 private 생성자나 열거 타입으로 싱글턴임을 보증하라
- 싱글턴이란, 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.
- 싱글턴을 만드는 방법
  - public static 필드 방식 
    - 초기화되고 나면 고정됨.
    - 의도하지 않은 객체 생성 방지
  - private static final 필드 방식
    - 생성을 private로 막고, public static 메서드(getInstance를 구현)로 객체가 있으면 반환 없으면 생성
  - Enum 방식 
    - public 필드 방식과 비슷하지만, 더 간결하고 직렬화 할 수 있다.
    ###### 직렬화란 객체를 파일로 저장하거나 네트워크로 전송할 수 있게 하는 것을 말한다. 이와 반대로 역직렬화는 파일로 저장된 객체를 다시 객체로 변환하는 것을 말한다.

### 아이템 4 인스턴스화를 막으려거든 private 생성자를 사용하라
- 유틸리티 클래스는 객체를 만들어 사용하지 않지만 실수로 객체를 생성할 수 있으므로, private으로 생성자를 만들어 인스턴스화를 막아야 한다.
###### 유틸리티 클래스란, 객체의 생성 없이 사용할 수 있는 메서드로만 구성된 클래스를 말한다. ex) Math, Arrays
### 아이템 5 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
- 의존 객체 주입이란, 클래스가 사용하는 자원을 직접 생성하지 않고, 외부에서 주입받아 사용하는 방법을 말한다.
- 예시
  - 스프링에서 데이터 베이스 연결시, DB의 정보를 직접 명시하지 않고, @Configuration을 사용하여 yml(외부)에서 주입받아 사용한다.

### 아이템 6 불필요한 객체 생성을 피하라
- Boxing 타입(Integer)이 아님 기본 타입(int)을 사용하라
  - Boxing 타입을 쓰는 순간 관련 인스턴스가 생성되어 메모리 사용을 하며, 10배정도 느리다.
  - Integer sum=0; 처럼 Boxing 타입을 쓰면 sum++할 때마다 새로운 Integer 객체가 생성된다. 기본 타입 int를 사용하면 객체 생성이 없어 빠르다.
  - int의 default 값은 0이지만, Integer의 default 값은 null이다. 따라서, Integer를 사용할 때는 null 체크를 해야 한다.

```java
static boolean isRomanNumeral(Stirng s) {
  return s.matches("^(?=.)M*(C[MD]|D?C{0,3})");
}
```
- 해당 객체를 계속해서 생성
```java
static boolean isRomanNumeral(Stirng s) {
    private static final Pattern ROMAN = Pattern.compile(
        "^(?=.)M*(C[MD]|D?C{0,3})"
    );
  return ROMAN.matcher(s).matches();
}
```
- 한번만 생성하고 재사용
### 아이템 7 다 쓴 객체 참조를 해제하라

```java
public class Stack{
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    
    public Stack(){
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
  // 생략
    public Object pop(){
        if(size == 0){
            throw new EmptyStackException();
        }
        return elements[--size]; // 다 쓴 참조를 해제하지 않고 top의 참조만 변경
    }
}
```
```java
public Object pop(){
    if(size == 0){
        throw new EmptyStackException();
    }
    Object result = elements[--size];
    elements[size] = null; // 다 쓴 참조 해제. 이로써 GC가 수거 가능
    return result;
}
```
- 다 쓴 참조를 해제하지 않으면, GC가 수거하지 않아 메모리 누수가 발생할 수 있다.
### 아이템 8 finalizer와 cleaner 사용을 피하라
- finalizer와 cleaner는 객체가 소멸될 때 호출되는 메서드이다.
- finalizer은 예측할 수 없고, 위험하며, 일반적으로 느리다. clearner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리다.
- finalizer와 cleaner는 즉시 수행되지 않으므로, 자원을 즉시 해제할 수 없다.

### 아이템 9 try-finally보다는 try-with-resources를 사용하라
- try-finally은 try문을 빠져나갈 때, finally문이 실행되며, 에외가 발생하더라도 실행된다.
- finally에 close()를 사용하여 자원을 해제한다.
- try-with-resources는 AutoCloseable 인터페이스를 구현한 자원을 사용할 때, try()안에 자원을 선언하면, try문을 빠져나갈 때 자동으로 close()를 호출하여 자원을 해제한다.
---
# 가비지 컬렉션이란
- 동적으로 할당한 메모리 중 더 이상 사용되지 않는 메모리를 해제하는 프로세스.
- GC를 실행하기 위해 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개되는 Stop the World 특징을 가지고 있다.
- 장점
  - 메모리 누수를 방지하며, 메모리를 효율적으로 사용할 수 있다.
  - 메모리를 자동으로 관리하므로, 개발자가 메모리 해제를 직접하지 않아도 된다.
- 단점
  - 언제 메모리 해제가 되는지 알 수 없으며, 가비지 컬렉션 실행시간동안 모든 스레드가 멈춰, 프로그램의 성능에 영향을 줄 수 있다. -> 멈추지 말아야할 프로그램의 메모리가 해체되어, 프로그램이 종료될 수 있다.
  - 어떤 메모리를 해제할지 결정하는 비용이 든다.
- 힙

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc37SjY%2FbtqDWT3iMNg%2Fh5HsTIvL7NPssTppNAHhXk%2Fimg.png" width="70%">

- Young 영역
  - Eden 영역 : 새로 생성된 객체가 할당되는 영역
  - Survivor 영역 : 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역
- Old 영역
  - Young 영역에서 살아남은 객체가 일정 시간이 지나면 Old 영역으로 이동한다.
- Permanent 영역
  - 응용프로그램에서 사용되는 클래스와 함수를 설명하기 위한 메타데이터를 보관하는 영역
- 처리 방식
  - Mark and Sweep
    - Mark : 사용 중인 객체들을 마크하여, 사용되지 않는 객체를 식별하는 작업
    - Sweep : Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업
    - Old 영역에서 주로 사용되는 GC로, 중단 시간이 비교적 길다.
  - Minor GC
    - Young 영역에서의 주로 사용되는 GC로, Young 영역에서 가비지 객체만 수집하므로 중단 시간이 짧음

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FGleJP%2FbtqDWr0j7SV%2FEczLkocDNoOreFYR4MLaA0%2Fimg.png" width="70%">
  
    - 새로 생성된 객체가 Eden 영역에 할당된다.
  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FIZ0eu%2FbtqDVWMZ4i4%2FqYkOkTAiN2ky6HbKU8kcF1%2Fimg.png" width="70%">

    - Eden 영역이 가득 차게 되면, Minor GC가 실행되면서 Eden 영역에서 사용되지 않는 객체의 메모리를 해제한다.
    - Eden 영역에서 살아남은 객체는 Survivor 영역으로 이동된다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpJxQ8%2FbtqDWrTwPg8%2F5I9GkbrnxP9cPKONksGwOk%2Fimg.png" width="70%">

    - Survivor 영역이 가득 차게 되면, 살아남은 객체를 다른 Survivor 영역으로 이동시킨다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbw8e2U%2FbtqDVdIsngg%2Fk45xoXaQZ1FspuUG7NkiTk%2Fimg.png" width="70%">
  
    - Survivor 영역에서 살아남은 객체는 Old 영역으로 이동된다.

# StringBuilder와 StringBuffer의 차이
<img src="https://velog.velcdn.com/images%2Fskyepodium%2Fpost%2F22bc853f-5b76-4824-a705-6420d8adbb13%2Fimage.png" width="70%">

- 공통점 : 문자열을 연산(추가, 변경)할 때 주로 사용하는 자료형
- 차이점 :
  - String
  
  <img src="https://velog.velcdn.com/images%2Fskyepodium%2Fpost%2Ffe9a6c49-2656-459d-a6b4-dd9fa4444b06%2Fimage.png" width="70%">
  
    - String 객체의 값은 변경되지 않고 새로운 객체를 생성한다.(불변)
    - 속도가 가장 느리므로, 문자열 연산이 많은 경우 사용하지 않는다.
###### String Constant Pool 
###### Java에서 동일한 문자열을 효율적으로 관리하기 위해 JVM이 사용하는 메모리 영역이며, 힙 메모리의 일부이다. 예를 들어 "ABC"이라는 문자열 리터럴이 프로그램 내에서 여러 번 사용되더라도, 서로 다른 메모리 위치에 저장되지 않고 String Constant Pool의 같은 메모리 주소를 참조한다. 이를 통해 메모리를 절약하고 성능을 향상할 수 있다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeVviG0%2Fbtsg0mNkhJo%2FfMRAQM4HfBcd0yen1zPK4K%2Fimg.png" width="70%">

  - StringBuffer
    - 동기화를 지원하며, 각 메서드별로 Synchronized Keyword가 존재해 멀티 스레드 환경에서도 사용할 수 있다.
    - 가변적이므로 문자열 추가 연산이 많고 멀티 스레드 환경일 경우 사용한다.
  - StringBuilder
    - 동기화를 지원하지 않으며, 단일 스레드 환경에서 사용한다.(스레드 세이프 X)
    - 동기화하지 않아, 연산 속도가 빠름
    - 문자열 추가 연산이 많고 단일 스레드 환경일 경우 사용한다.
