# 아이템 16 : public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

## 캡슐화의 이점을 제공하지 못하는 클래스

: 인스턴스 필드만을 모아놓은 클래스는 데이터에 직접 접근할 수 있으나 캡슐화의 이점을 제공하지 못한다.

```java
public class Point {
    public double x;
    public double y;
}
```

- API를 수정하지 않고는 내부 표현(값)을 바꿀 수 없다.
    
    → getter/setter가 존재해야 내부 표현 변경이 가능하다.
    
- 불변식을 보장하지 못한다.
    
    → 클라이언트가 데이터를 변경할 수 있다.
    
- 외부에서 필드에 접근할 때 부수작업을 수행할 수 없다.
    
    → 1차원적인 접근만 가능하고, 추가 로직을 삽입할 수 없다.
    

## 객체지향적인 클래스 작성 : 데이터 캡슐화

- 필드를 private로 변경
- public 접근자(getter)를 추가한다.

```java
public class Point {
	private int x;
	private int y;

	public Point(int x, int y) {
		this.x = x;
		this.y = y;
	}

	public int getX() { return x; }
	public int getY() { return y; }

	public void setX(int x) { this.x = x; }
	public void setY(int y) { this.y = y; }
}
```

- getter / setter 로 내부표현 변경이 가능하다.
    
    → getter/setter 메서드를 통해 언제든지 내부 표현을 변경할 수 있으므로 **유연성**을 얻는다는 장점이 있다.
    
- 클라이언트는 public 메서드를 통해서만 데이터 접근이 가능하다.
- 외부에서 부수작업을 수행시킬 수 있다.

## package-private클래스와 private 중첩 클래스의 데이터 캡슐화

package-private 클래스 또는 private 중첩 클래스라면 데이터 필드를 노출한다 해도 클래스가 표현하려는 추상 개념만 올바르게 표현해주면 문제가 없다.

```java
public class ToPoint {
	// public 클래스 내에 private static 클래스
	private static class Point {
		public double x;
		public double y;
	}

	public Point getPoint() {
		Point point = new Point();
		point.x = 3.5;
		point.y = 4.5;
		return point;
	}
}
```

- ToPoint 클래스 : 얼마든지 Point클래스의 필드를 조작할 수 있다.
- 외부 클래스 : Point클래스의 필드에 직접 접근할 수 없다.

### 클래스 중첩의 장점

- 클래스를 중첩시키는 방식은 클래스 선언 면에서나 이를 사용하는 클라이언트 코드면에서나 **접근자 방식보다 깔끔하다.**
- 클래스를 중첩시키면 클라이언트 코드가 내부에 묶이기는 하지만 클라이언트도 이 클래스를 포함하는 패키지 안에서 동작하는 코드이므로 **패키지 바깥 코드를 손대지 않고 데이터 표현방식을 바꿀 수 있다.**

## public 클래스 필드 직접 노출 사례

자바 플랫폼 라이브러리에도 `public` 클래스의 필드를 직접 노출시키지 말라는 규칙을 어긴 사례가 존재한다. 

대표적으로 `java.awt.package` 클래스의 `Point`와 `Dimension` 클래스가 있다.

```java
    ...

    public Dimension getSize() {
		return size();
	}

	@Deprecated
	public Dimension size() {
		return new Dimension(width, height);
	}

    ...
```

### 불변 필드를 노출한 public 클래스

```java
public class Time {
    private static final int HOURS_PER_DAY = 24;
    private static final int MINUTES_PER_HOUR = 60;
    
    public final int hour;
    public final int minute;

    public Time(final int hour, final int minute) {
        this.hour = hour;
        this.minute = minute;
    }   
}
...
```

- `public` 클래스 필드가 불변이라면 직접 노출할 떄의 단점이 조금은 줄어들지만, 여전히 좋은 코드가 아니다.
- `public` 필드에 `final` 키워드를 추가해 불변으로 만들면 **불변식은 보장**할 수 있게 되지만 여전히 **API를 변경하지 않고는 표현 방식을 바꿀 수 없고 필드를 읽을 때 부수작업을 수행할 수 없다**는 단점은 변하지 않는다.

## 최종 정리

`public` 클래스는 절대 가변 필드를 직접 노출해서는 안된다. 

하지만 `package-private` 클래스나 `private` 중첩 클래스에서는 종종 필드를 노출하는 편이 나을 때도 있다.

https://velog.io/@alkwen0996/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C16-public-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%97%90%EC%84%9C%EB%8A%94-public-%ED%95%84%EB%93%9C%EA%B0%80-%EC%95%84%EB%8B%8C-%EC%A0%91%EA%B7%BC%EC%9E%90-%EB%A9%94%EC%84%9C%EB%93%9C%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC