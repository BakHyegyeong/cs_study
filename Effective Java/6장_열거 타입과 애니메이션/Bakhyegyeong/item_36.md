# 아이템 36 : 비트 필드 대신 EnumSet을 사용하라

# 비트 필드 열거 상수

열거한 값들이 단독이 아닌 집합으로 사용되어야할 경우, `EnumSet`이 나오기 전에는 **각 상수에 서로 다른 2의 거듭제곱 값을 할당한 정수 열거 패턴을 사용**했다.

```java
public class Text{
	public static final int STYLE_BOLD = 1 << 0;  //1
  public static final int STYLE_ITALIC = 1 << 1;  //2
  public static final int STYLE_UNDERLINE = 1 << 2;  //4
  public static final int STYLE_STRIKETHROUGH = 1 << 3;  //8
    
    public void applyStyles(int styles) {
        System.out.printf("Applying styles %s to text%n", styles);
	   }
}

public static void main(String[] args) {
    Text text = new Text();
    text.applyStyles(STYLE_BOLD | STYLE_ITALIC); // 0001 OR 0010 = 0011
}
```

비트 연산자 `OR`을 사용하면 **여러 상수를** **하나의 집합으로 모을 수 있다.** <br>
→ 이렇게 만들어진 집합을 비트 필드라고 한다.

## 비트 필드의 장단점

**장점**

비트별 연산을 사용해 합집합과 교집합 같은 집합 연산을 효율적으로 수행할 수 있다.

**단점**

1. 정수 열거 상수의 단점을 그대로 지닌다.
    
    → 타입 안전성이 없고 이름 공간의 충돌 문제 등
    
2. 비트 값 필드가 그대로 출력되었을 때 해석하기 어렵다.
3. 최대 몇 비트가 필요한지 예측해서 적절한 타입(int/long)을 선택해야 한다.

## EnumSet

열거 타입 상수의 값으로 구성된 집합을 효과적으로 표현해준다.

→ `Set` 인터페이스 자체를 구현하고 있기 때문에 타입 안전하며, 출력했을 때도 알아보기 쉽게 정보를 담고 있다는 장점이 있다.

```java
public class Text {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}

    // 어떤 Set을 넘겨도 되나, EnumSet 이 가장 좋다.
    public void applyStyles(Set<Style> styles) {
		    System.out.printf("Applying styles %s to text%n",
                Objects.requireNonNull(styles));
    }
}

public static void main(String[] args) {
    Text text = new Text();
    text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
}
```

## EnumSet 단점


`EnumSet` 은 불변이 불가능하다.

→ `Collections.unmodifiableSet` 으로 `EnumSet`을 감싸, 집합을 수정하려 하거든 예외를 터트리게 하는 방식으로 사용할 수 있는 방안은 존재한다.

## 핵심 정리

열거할 수 있는 타입을 한데 모아 집합 형태로 사용한다고 해도 비트 필드를 사용할 이유는 없다. `EnumSet` 클래스가 비트 필드 수준의 명료함과 성능을 제공하고, 열거 타입의 장점까지 주기 때문이다.