# 디자인 패턴 및 객체지향 설계 원칙
## 디자인 패턴이란?
- 소프트웨어 설계시 발생하는 문제를 해결하기 위한 일반적인 방법이다.

## 객체지향이란?
- 객체를 생성하고 객체 간의 상호작용을 통해 프로그래밍을 하는 것이다.
- 객체지향 설계 원칙
  - SRP(Single Responsibility Principle): 클래스는 단 하나의 책임을 가져야 한다.
  - OCP(Open-Closed Principle): 확장에는 열려있고, 수정에는 닫혀있어야 한다.
  - LSP(Liskov Substitution Principle): 자식 클래스는 부모 클래스에서 가능한 행위를 수행할 수 있어야 한다.
  - ISP(Interface Segregation Principle): 클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다.
  - DIP(Dependency Inversion Principle): 고수준 모듈은 저수준 모듈에 의존하면 안된다. 둘 다 추상화에 의존해야 한다.

## Singleton 패턴이란? 왜 사용하는가?
- 인스턴스를 하나만 생성되도록 보장하고, 해당 인스턴스에 접근할 수 있는 전역적인 접근점을 제공하는 패턴이다.
- 사용 이유
  - 리소스를 절약할 수 있다.
  - 한 인스턴스 공유를 통해 데이터를 일관성 있게 유지할 수 있다.
## Adapter 패턴이란? 왜 사용하는가?
- 말 그대로 서로 다른 안터페이스를 가진 클래스를 연결해주는 역할을 한다.
- 사용 이용
  - 호환되지 않는 인터페이스를 사용하고자 할 때 사용한다.
  - 기존 코드를 수정하지 않고도 새로운 코드를 재사용할 수 있다.
## Template Method 패턴이란? 왜 사용하는가?
- 상위 클래스가 전체적인 탬플릿을 제공하고, 하위 클래스가 구체적인 내용을 결정하는 패턴이다.
- 사용 이유
  - 코드 중복을 줄일 수 있다.
  - 프로세스 흐름을 일관적으로 유지할 수 있다.
## Observer 패턴이란? 왜 사용하는가?
- 옵저버가 객체를 관찰하다가 변화가 생기면 연관된 다른 객체들에게 통보하는 패턴이다.
- 사용 이유
  - 객체 간의 의존성을 줄일 수 있다.
  - 객체 간의 상호작용을 통해 느슨한 결합을 유지할 수 있다.
## Java에서 Builder 패턴을 사용하는 이유는?
- 객체 생성시에 매개변수가 많을 때 사용한다.
- 사용 이유
  - 객체 생성시에 매개변수가 많을 때 가독성을 높일 수 있다.
  - 객체 생성시에 매개변수의 순서를 신경쓰지 않아도 된다.
## Java에서 팩토리 메서드 패턴을 사용하는 이유는?
- 객체 생성을 서브 클래스에서 결정하도록 하는 패턴이다.
- 사용 이유
  - 객체 생성을 서브 클래스에서 결정하도록 하여 객체 생성을 유연하게 할 수 있다.
  - 객체 생성을 서브 클래스에서 결정하도록 하여 객체 생성을 캡슐화할 수 있다.
```java
interface Shape {
    void draw();
}

class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Circle");
    }
}

class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Rectangle");
    }
}

class ShapeFactory {
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        }
        return null;
    }
}
```
