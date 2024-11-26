# 제네릭(Generic)이란
제네릭은 클래스나 메서드에 사용할 **타입을 미리 지정하지 않고, 객체를 생성하거나 메서드를 호출 할 때 타입을 지정**해서 사용하는 기능이다.
장점 : 타입 안정성을 보장해주고 코드의 중복을 줄일 수 있다.

### 제네릭 클래스 만들기

```java
public class Box<T> {
    private T content;

    public void setContent(T content) {
        this.content = content;
    }

    public T getContent() {
        return content;
    }
}
```

제네릭 클래스를 정의할때는 위와 같이 타입을 미리 지정하지 않고 **타입 매개변수 T**를 사용한다.

```java
Box<String> stringBox = new Box<>();
stringBox.setContent("Hello");

Box<Integer> intBox = new Box<>();
intBox.setContent(123);
```

Box 클래스는 어떤 타입이든 사용할 수 있도록 설계되어서, 위에 보이는 코드처럼 String이든 Integer든 **객체를 생성할 때 타입을 지정**해서 넣어준다.
