## 오버로딩과 오버라이딩

### 오버라이딩

: 상속받은 **메소드의 내용을 자식 클래스에서 변경**하는 것

→ 부모 클래스의 상속을 받은 자식 클래스에서 확장하는 개념

- 메소드의 이름이 일치해야 함
- 메소드 매개변수의 개수, 순서 그리고 데이터 타입 일치해야 함
- 메소드의 return 타입이 일치해야 함

### 오버로딩

**: 같은 이름의 메소드**를 **매개변수의 타입이나 개수**를 다르게 하여 여러 번 정의하는 것

→ 하나의 클래스 내부에서 확장하는 개념

- 메소드의 이름이 일치해야 함
- 메소드 매개변수의 개수 또는 타입이 달라야 함 (개수가 같다면 타입, 타입이 같다면 개수를 다르게 해야함)
- 메소드의 return 타입이 달라야 함

### 오버라이딩, 오버로딩 예시

```java
class Shape {
		// 오버로딩
    void draw() {
        System.out.println("Drawing Shape");
    }
    // 오버로딩
    void draw(String color) {
        System.out.println("Drawing " + color + " Shape");
    }
}
class Circle extends Shape {
		// 오버라이딩
    void draw() {
        System.out.println("Drawing Circle");
    }
}
```

## 추상 클래스와 인터페이스의 차이점

| / | abstract class | interface |
| --- | --- | --- |
| 구성 | 변수 + 추상메서드 or 일반메서드 | 상수 + 추상메서드 |
| 객체 생성 | 불가능함 | 불가능함 |
| 추상메서드에 abstract | 표시해야함 | 생략가능함 |
| 상속/구현 | 다중상속이 불가능함 | 다중구현이 가능함 |
