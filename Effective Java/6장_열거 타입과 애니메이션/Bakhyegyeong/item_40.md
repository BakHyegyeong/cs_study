# 아이템 40 : @Override 애너테이션을 일관되게 사용하라

# @Override

메서드 선언이 **상위 타입의 메서드를 재정의했다는 것**을 알려주는 애너테이션이다.

### 메서드 재정의 조건

- 상속 관계에서 발생한다.
- 접근 제어자, 리턴 타입, 매개변수가 모두 일치해야 한다.
- 접근 제어자는 확장은 가능하지만 축소는 불가능하다.
    
    → 상위 클래스에 접근 제어자가 `protected`인 메서드를 재정의할 경우 `protected`, `public`으로 가능하다.
    

## 사용 예시

아래 코드는 영어 알파벳 2개로 구성된 문자열을 표현하는 클래스이다.

```java
public class Bigram {
    private final char first;
    private final char second;

    public Bigram(char first, char second) {
        this.first  = first;
        this.second = second;
    }

    // 오버라이드가 아닌 오버로딩
    public boolean equals(Bigram b) {
        return b.first == first && b.second == second;
    }

    public int hashCode() {
        return 31 * first + second;
    }

    public static void main(String[] args) {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++)
            for (char ch = 'a'; ch <= 'z'; ch++)
                s.add(new Bigram(ch, ch));
        System.out.println(s.size());
    }
}
```

위 코드에서 문제는 equals 메소드를 재정의(`overriding`)한 게 아니라 **다중정의(`overloading`)** 해버린 것이다. 

→ Object의 `equals()` 를 재정의하려면 **매개변수 타입을 Object로** 해야 하는데 그렇지 않아서 상속이 아닌 **별개의 equals 메서드를 정의**한 꼴이 되었다.

## 오류 수정

```java
@Override 
public boolean equals(Object o) {
    if (!(o instanceof Bigram2))
        return false;
    Bigram2 b = (Bigram2) o;
    return b.first == first && b.second == second;
}
```

위와 같은 오류를 막기 위해서 **상위 클래스의 메서드를 재정의하려는 모든 메서드에는 @Override 애너테이션을 사용**한다.

## **인터페이스와 @Override**

`@Override` 는 클래스 뿐만 아니라, 인터페이스의 메서드를 재정의 할 때도 사용할 수 있다. 

→ 인터페이스에서 디폴트 메서드를 지원하면서, **인터페이스 메서드를 구현한 메서드에도 `@Override`를 다는 습관**을 들이는 것은 좋은 일이다.

```java
// 인터페이스
public interface State {
    State draw(Card card);
    boolean isRunning();
}

// 사용
public final class Ready implements State {
    ...
    @Override
    public State draw(Card card) {
        cards.append(card);
					...
        return new Ready(cards);
    }

    @Override
    public boolean isRunning() {
        return false;
    }
}
```

## 핵심 정리

재정의한 모든 메서드에 @Override 애너테이션을 의식적으로 달면, 여러분이 실수했을때 컴파일러가 바로 알려줄 것이다. 

예외는 한 가지 뿐이다. 구체 클래스에서 상위 클래스의 추상 메서드를 재정의한 경우에는 이 애너테이션을 달지 않아도 된다.(달아도 상관 없다)