# 아이템 23 : 태그 달린 클래스보다는 클래스 계층구조를 활용하라

# 태그 달린 클래스

**하나의 클래스가 두 가지 이상의 의미를 표현** 가능할 때, 그중 현재 표현하는 의미를 태그 값으로 알려주는 클래스

```java
class Figure {
    enum Shape { RECTANGLE, CIRCLE };  // TAG 

    // 태그 필드 - 현재 모양을 나타낸다.
    final Shape shape;

    // 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
    double length;
    double width;

    // 다음 필드는 모양이 원(CIRCLE)일 때만 쓰인다.
    double radius;

    // 원용 생성자
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    // 사각형용 생성자
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch(shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
```

## 태그 달린 클래스의 단점

1. 열거 타입 선언, 태그 필드, `switch` 문 등 쓸데없는 코드로 가독성이 떨어진다.
2. 다른 의미를 위한 코드도 해당하지 않는데 항상 존재하니 메모리를 많이 먹는다.
3. 필드들을 불변을 위해 `final` 로 선언하려면 해당 의미에 쓰이지 않는 필드들까지 생성자에서 초기화해야한다.
4. 또 다른 의미를 추가하려면 `switch` 문 코드를 수정해야 하는 등의 쓸데없는 코드가 늘어난다.
5. 인스턴스의 타입만으로는 현재 나타내는 의미를 알 길이 전혀 없다.

태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다.

## 클래스 계층 구조

**추상 클래스와 추상 메서드**를 통해 **타입 하나로 다양한 의미의 객체를 표현**할 수 있게 해준다.

### 클래스 계층 구조 생성 과정

1. 계층 구조의 루트가 될 추상 클래스 정의한다.

    ```java
    abstract class Figure {
        final String color;
        
        public Figure(String color){
            this.color = color;
        }
        
        final String getColor() {
            return color;
        }
        
        abstract double area();
    }
    ```

   - 태그 값에 따라 **동작이 달라지는 메서드**들을 루트 클래스의 **추상 메서드**로 선언
       
       → `abstract double area();`
       
   - 태그 값에 상관없이 **동작이 일정한 메서드와 공통 메서드**들을 루트 클래스에 **일반 메서드**로 선언
       
       → `String getColor(){}`
       
   - **공통으로 사용하는 데이터 필드는 모두 추상 클래스에 선언하자.**
       
       → `String color`
    

2. **태그 별로 추상 클래스를 확장한 구현 클래스**를 선언한다.
    - 원
        
        ```java
        class Circle extends Figure {
            final double radius;
        
            Circle(String color, double radius) { super(color); this.radius = radius; }
        
            @Override double area() { return Math.PI * (radius * radius); }
        }
        ```
        
    - 사각형
        
        ```java
        class Rectangle extends Figure {
            final double length;
            final double width;
        
            Rectangle(String color, double length, double width) {
                super(color);
                this.length = length;
                this.width  = width;
            }
            @Override double area() { return length * width; }
        }
        ```
        

### 클래스 계층 구조의 장점

- 위에서 이야기한 쓸데없는 코드가 모두 사라졌다.
- 남은 필드들은 모두 `final`로 선언할 수 있으므로 생성과 동시에 불필요한 값이 생기지 않는다.
- 만약 삼각형(`Triangle`) 태그를 추가할 예정이었다면, `Shape`과 `Switch`를 사용하지 않고 `Figure` 구현 클래스를 만들면서 쉽게 구현 가능하다.
- 각 타입 사이에 자연스러운 계층 관계를 반영할 수 있어서 유연성은 물론 컴파일타임에서 타입 검사 능력을 높여준다.

## 핵심 정리

태그를 통해서 클래스를 사용하는 것보다는 계층 구조를 활용하는 것이 훨씬 좋다.