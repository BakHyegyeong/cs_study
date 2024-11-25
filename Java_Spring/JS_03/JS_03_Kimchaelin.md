# 오버로딩과 오버라이딩의 차이
## 오버로딩
- 같은 이름의 메소드를 여러개 정의하는 것(상속 무관)
  - 매개변수 타입이나 개수가 다르면 같은 이름의 메소드를 여러개 정의할 수 있다. (리턴 타입, 접근 제어자는 오버로딩 조건에 포함되지 않는다.)
  - ex) `int add(int a, int b)`와`int add(int a, int b, int c)` / `person add(String name, int age)`와 `person add(String name, String address)`
  - 상속 관계든 아니든 상관없이 오버로딩은 가능하다.
  - 오버로딩은 컴파일 시간에 결정된다.
- 장점
  - 메소드 이름을 일관성 있게 사용할 수 있다.(이름만 보고 어떤 기능인지 유추 가능)
- 단점
  - 메소드 이름만 보고 어떤 메소드를 호출할지 알기 어렵다.

## 오버라이딩
- 상위 클래스의 메소드를 하위 클래스에서 재정의하는 것(상속 관계에서만 발생)
  - 상위 클래스의 메소드와 메소드명, 매개변수 타입, 개수 등이 동일해야 한다.
  - 접근 제어자는 상위 클래스의 메소드보다 같거나 넓은 범위로 변경할 수 있다.
  - 예외는 상위 클래스의 메소드보다 적거나 같은 개수로 선언할 수 있다.
  - 오버라이딩은 런타임 시간에 결정된다.
- 장점
  - 상위 클래스의 메소드를 재정의하여 하위 클래스에서 다른 기능을 구현할 수 있다.
  - 다형성을 구현할 수 있다.
- 단점
  - 상위 클래스의 메소드를 재정의하면 상위 클래스의 메소드가 숨겨진다.

# 추상 클래스와 인터페이스의 차이
<img src="https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwVZqF%2FbtsGR3avFhf%2FbeSS5N8MbQDQsJk56Fd3n1%2Fimg.png">

## 추상 클래스(abstract class)
- 일부 메소드가 구현되지 않은 미완성 설계도이다. 상속을 통해서 자식 클래스 기능 확장을 위함이다. 
  - 추상 클래스는 객체를 생성할 수 없다.
  - 추상 메서드와 일반 메소드가 포함할 수 있다.
  - 추상 클래스는 단일 상속만 가능하다. ex) `class A extends B` 가능 `class A extends B, C` 불가능
    - 이유는 다중 상속을 허용하면 두 클래스의 메소드가 충돌할 수 있기 때문이다.

## 인터페이스
- 어느정도 구현된 기본 설계도이다. 기능을 제공하고자 할 때 사용한다.
  - 객체를 생성할 수 없다.
  - 상수와 추상 메소드만 포함할 수 있다.
      - JDK 1.8부터 default 메소드와 static 메소드를 포함할 수 있다.
  - 다중 상속이 가능하다. ex) `interface A extends B, C` 가능
    - 다중 상속을 허용하기 때문에 다양한 기능을 제공할 수 있다.

```java
// 추상클래스 : 필요한 메소드를 자식 클래스에서 구현하도록 강제
abstract class Animal {
    abstract void makeSound();
}

// 인터페이스 : 할 수 있는 기능을 정의
interface Pet {
    void play(){
        System.out.println("Playing");
    }
    
    void eat();
}

class Dog extends Animal implements Pet {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }

    @Override
    public void play() {
        System.out.println("Playing dog");
    }
    
    @Override
    public void eat() {
        System.out.println("Eating dog food");
    }
}

public class Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.makeSound(); // 출력: Bark
        dog.play(); // 출력: Playing fetch
    }
}
```
