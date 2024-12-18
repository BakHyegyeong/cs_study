# 아이템 38 : 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

# 열거 타입 확장

열거 타입을 확장하는 것은 불가능하지만 **인터페이스를 활용**해  확장할 수 있다.

→ 열거 타입 확장은 대부분의 상황에서 안좋지만 **사용자 확장 연산을 추가**하는 등의 상황에서는 필요하다.

## 타입 안전 열거 패턴

열거 상수마다 **인스턴스를 생성 후 인스턴스의 주소값을 기준**으로 로직을 수행한다.

```java
// 클래스 내의 상수마다 인스턴스를 생성
public class ClassGrade {
    public static final ClassGrade BASIC = new ClassGrade();
    public static final ClassGrade GOLD = new ClassGrade();
    public static final ClassGrade DIAMOND = new ClassGrade();
}

public class DiscountService {

		// 입력받은 상수의 클래스 참조값(힙 영역에 저장된 주소값)을 비교
    public int discount(ClassGrade classGrade, int price) {
        int discountPercent = 0;

        if (classGrade == ClassGrade.BASIC) {
            discountPercent = 10;
        } else if (classGrade == ClassGrade.GOLD) {
            discountPercent = 20;
        } else if (classGrade == ClassGrade.DIAMOND) {
            discountPercent = 30;
        } else {
            System.out.println("할인X");
        }

        return price * discountPercent / 100;
    }
}
```

**장점**

- **타입 안전성 향상 :** 정해진 객체만 사용할 수 있기 때문에, 잘못된 값을 입력하는 문제를 근본적으로 방지할 수 있다.
- **데이터 일관성 :** 정해진 객체만 사용하므로 데이터의 일관성이 보장된다.
- **제한된 인스턴스 생성 :** 클래스는 사전에 정의된 몇 개의 인스턴스만 생성하고, 외부에서는 이 인스턴스들만 사용할 수 있도록 한다.

**단점**

- 이 패턴을 구현하려면 위와 같이 많은 코드를 작성해야 한다.
- private 생성자를 추가하는 등 유의해야 하는 부분들도 있다.

참조 - https://madeprogame.tistory.com/69

## 인터페이스를 이용한 열거 타입 확장

```java
// 인터페이스 정의
public interface Operation {
    double apply(double x, double y);
}

// 인터페이스를 구현한 열거 타입
public enum BasicOperation implements Operation {
		// 열거 상수별 apply 메서드 구현
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override public String toString() {
        return symbol;
    }
}
```

열거 타입에 따로 추상 메서드를 선언하지 않아도 된다는 장점이 있다.

## 확장 열거 타입의 사용

**확장된 열거 타입의 모든 상수를 출력하는 메서드**를 구현하는데 두 가지 방법이 있다.

### 열거 타입의 Class 객체를 이용

```java
public static void main(String[] args) {
       double x = Double.parseDouble(args[0]);
       double y = Double.parseDouble(args[1]);
       test(ExtendedOperation.class, x, y);  //class 리터럴
}

private static <T extends Enum<T> & Operation> void test(
            Class<T> opEnumType, double x, double y) {
       for (Operation op : opEnumType.getEnumConstants())
           System.out.printf("%f %s %f = %f%n",
                   x, op, y, op.apply(x, y));
}
```

- `getEnumConstants()` : Enum 클래스에서 정의된 **모든 상수를 배열 형태로 반환**하는 메서드
- `<T extends Enum<T> & Operation>` : Class 객체가 **열거 타입인 동시에 Operation 인터페이스의 하위 타입**이어야 한다는 것을 의미

### 컬렉션 인스턴스를 이용

```java
public static void main(String[] args) {
        double x = Double.parseDouble(args[0]);
        double y = Double.parseDouble(args[1]);
        // values()가 반환하는 열거 타입 상수를 List로 변환
        test(Arrays.asList(ExtendedOperation.values()), x, y);
}
private static void test(Collection<? extends Operation> opSet,
                             double x, double y) {
        for (Operation op : opSet)
            System.out.printf("%f %s %f = %f%n",
                    x, op, y, op.apply(x, y));
}
```

- `Collection<? extends Operation>` : Operation의 하위 타입임을 보장

## 인터페이스 이용의 문제점

**같은 인터페이스를 구현하는 열거 타입끼리는 상속할 수 없다.**

→ 공통된 부분이 많지 않다면 같은 인터페이스 내에서 디폴트 메서드로 구현한다.

→ 공통된 부분이 많다면 별도의 도우미 클래스나 정적 도우미 메서드로 분리한다.

## 핵심 정리

열거 타입 자체는 확장할 수 없지만, 인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있다. 

이렇게 하면 클라이언트는 이 인터페이스를 구현해 자신만의 혹은 다른 열거 타입을 생성할 수 있다.