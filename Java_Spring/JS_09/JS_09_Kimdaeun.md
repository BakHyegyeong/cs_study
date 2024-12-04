# 객체지향의 4가지 특징
## 1. **추상화 (Abstraction)**
- 객체의 공통적인 속성과 동작을 추출하여 정의.
- 복잡성을 줄이고 중요한 부분에만 집중할 수 있도록 설계한다.
## 2. **캡슐화 (Encapsulation)**
- 객체의 속성과 메서드를 하나로 묶고, 외부에서의 직접 접근을 제한
- **장점** : 데이터 보호, 코드의 변경이 다른 부분에 영향을 미치지 않는다.
- ex. `private`로 변수 선언하고 `getter`와 `setter`를 통해서 접근해야한다.
## 3. **상속 (Inheritance)**
- 부모 클래스에서 속성과 메서드를 물려받아 자식 클래스를 생성한다.
- 코드 재사용성과 확장성을 제공한다.
## 4. **다형성 (Polymorphism)**
- 같은 이름의 메서드나 객체가 다양한 형태로 동작
- ex. 메서드 오버로딩, 오버라이딩

# 설계의 5가지 원칙 (SOLID)
## 1. **단일 책임 원칙 (SRP - Single Responsibility Principle)**
- 클래스는 하나의 책임만 가져야 한다.
## 2. **개방-폐쇄 원칙 (OCP - Open/Closed Principle)**
- 확장에는 열려 있고, 수정에는 닫혀 있어야 한다.
## 3. **리스코프 치환 원칙 (LSP - Liskov Substitution Principle)**
- 자식 클래스는 부모 클래스를 대체할 수 있어야 한다.
## 4. **인터페이스 분리 원칙 (ISP - Interface Segregation Principle)**
- 클라이언트는 자신이 사용하지 않는 메서드에 의존하지 않아야 한다.
## 5. **의존 역전 원칙 (DIP - Dependency Inversion Principle)**
- 고수준 모듈은 저수준 모듈에 의존해서는 안 되며, 둘 다 추상화에 의존해야 한다.
