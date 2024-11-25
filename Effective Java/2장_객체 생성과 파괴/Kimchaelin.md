# 2장 객체 생성과 파괴
## 아이템 1 생성자 대신 정적 팩터리 메서드를 고려하라
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

## 아이템 2 생성자에 매개변수가 많다면 빌더를 고려하라
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

## 아이템 3 private 생성자나 열거 타입으로 싱글턴임을 보증하라
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

## 아이템 4 인스턴스화를 막으려거든 private 생성자를 사용하라
- 유틸리티 클래스는 객체를 만들어 사용하지 않지만 실수로 객체를 생성할 수 있으므로, private으로 생성자를 만들어 인스턴스화를 막아야 한다.
###### 유틸리티 클래스란, 객체의 생성 없이 사용할 수 있는 메서드로만 구성된 클래스를 말한다. ex) Math, Arrays
## 아이템 5 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
- 의존 객체 주입이란, 클래스가 사용하는 자원을 직접 생성하지 않고, 외부에서 주입받아 사용하는 방법을 말한다.
- 예시
    - 스프링에서 데이터 베이스 연결시, DB의 정보를 직접 명시하지 않고, @Configuration을 사용하여 yml(외부)에서 주입받아 사용한다.

## 아이템 6 불필요한 객체 생성을 피하라
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
## 아이템 7 다 쓴 객체 참조를 해제하라

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
## 아이템 8 finalizer와 cleaner 사용을 피하라
- finalizer와 cleaner는 객체가 소멸될 때 호출되는 메서드이다.
- finalizer은 예측할 수 없고, 위험하며, 일반적으로 느리다. clearner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리다.
- finalizer와 cleaner는 즉시 수행되지 않으므로, 자원을 즉시 해제할 수 없다.

## 아이템 9 try-finally보다는 try-with-resources를 사용하라
- try-finally은 try문을 빠져나갈 때, finally문이 실행되며, 에외가 발생하더라도 실행된다.
- finally에 close()를 사용하여 자원을 해제한다.
- try-with-resources는 AutoCloseable 인터페이스를 구현한 자원을 사용할 때, try()안에 자원을 선언하면, try문을 빠져나갈 때 자동으로 close()를 호출하여 자원을 해제한다.

```java
import java.io.BufferedInputStream;
import java.io.BufferedWriter;

static String firstLineOfFile(String path) throws IOException {
    try (
        FileInputStream fis = new FileInputStream(path);
        BufferedInputStream bis = new BufferedInputStream(fis);
    ) {
        int date;
        while ((date = fis.read()) != -1) {
            System.out.println((char) date);
        }
    }
}
```
