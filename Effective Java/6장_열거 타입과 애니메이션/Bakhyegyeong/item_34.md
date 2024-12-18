# 아이템 34 : int 상수 대신 열거 타입을 사용하라

# 열거 타입

일정 개수의 상수 값을 정의한 후 그 외의 값을 허용하지 않는 타입

→ 열거 타입이 생기기 이전에는 상수들을 선언해서 사용하였다.

## 정수 열거 패턴

```java
public static final int APPLE_FUHI = 0;
public static final int APPLE_PIPPEN = 1;
public static final int APPLE_GRANNY_SMITH = 2;

public static final int ORANGE_NAVEL= 0;
public static final int ORANGE_TEMPLE = 1;
public static final int ORANGE_BLOOD = 2;
```

이렇게 사용하면 아래와 같은 단점이 존재한다.

1. 타입 안전을 보장할 방법이 없으며 표현력도 좋지 않다.
    
    → 오렌지를 건네야 할 메서드에 사과를 보내고 동등 연산자(`==`)로 비교하더라도 아무런 경고 메시지를 출력하지 않는다.
    
2. 상수의 값이 바뀌면 클라이언트도 반드시 다시 컴파일해야 한다.
3. 정수 상수는 숫자로 표현되기 때문에 toString과 같이 의미가 담겨 있는 문자열로 출력하기 힘들다.

## 열거 타입

```java
public enum Apple {FUJI, PIPPN, GRANNY_SMITH}
public enum Orange {NAVEL, TEMPLE, BLOOD}
```

### 열거 타입의 특징

1. 열거 타입은 인스턴스 통제된다.

    열거 타입은 클래스이며 상수 하나당 자신의 인스턴스를 하나씩 만들어 **public static final 필드로 공개**한다.

    → 각 상수의 **생성자를 제공하지 않아** 클라이언트가 직접 생성하거나 확장할 수 없어 **인스턴스들은 하나씩만 존재**한다.

    → 열거 타입 클래스의 **static 변수들은 JVM 메모리 영역의 메서드 영역**에, **static 변수들의 인스턴스는 힙 영역에 저장**된다.

2. 열거 타입은 컴파일타입 타입 안전성을 제공한다.

    Apple 열거 타입을 매개변수로 받는 메서드를 선언했다면, 건네 받은 참조는 세 가지 값중 하나임이 보장된다.

    → 다른 타입의 값을 넘기면 컴파일 오류가 발생한다.

3. 열거 타입에는 각자의 이름 공간이 있어, 이름 중복을 허용한다.
    
    → 공개되는 것은 오직 필드의 이름뿐이지 값이 아니기 때문에, 열거 타입에 새로운 상수를 추가하거나 순서를 바꿔도 다시 컴파일하지 않아도 된다. 
    

1. 열거 타입에 임의의 메서드나 필드를 추가하거나 인터페이스를 구현할 수 있다.

## 메서드와 필드를 갖는 열거 타입

생성자에서 데이터를 받아 인스턴스 필드에 저장한다.

→ 특정 상수와 관련된 데이터를 Enum 클래스 안에 내재시킬 수 있다.

- 열거 타입은 불변이기 때문에 **내부의 모든 필드는 final이어야 한다.**
- 생성자가 public 일 경우 오류가 발생한다.
- `Enum.values()` : 자신 안에 정의된 상수들의 값을 배열에 담아 반환하는 정적 메서드
    
    → 값들은 선언된 순서대로 저장된다.
    

```java
public enum Planet {
    // 상수
    MERCURY(3.302e+23, 2.439e6),  
    VENUS  (4.869e+24, 6.052e6),
    EARTH  (5.975e+24, 6.378e6),
    MARS   (6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN (5.685e+26, 6.027e7),
    URANUS (8.683e+25, 2.556e7),
    NEPTUNE(1.024e+26, 2.477e7);

    private final double mass;           // 질량(단위: 킬로그램)
    private final double radius;         // 반지름(단위: 미터)
    private final double surfaceGravity; // 표면중력(단위: m / s^2)

    // 중력상수(단위: m^3 / kg s^2)
    private static final double G = 6.67300E-11;

    // 생성자
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }

    public double mass()           { return mass; }
    public double radius()         { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity;  // F = ma
    }
}
```

## 값의 변화에 따른 열거 타입

1. switch 문
    
    ```java
    public enum Operation {
       PLUS, MINUS, TIMES, DIVIDE;
       
       public double apply(double x, double y){
       	switch(this){
        	case PLUS : return x + y;
          case MINUS : return x - y;
            ...
        }
        throw new AssertionError("알 수 없는 연산 : " + this);
       }
    }
    
    // 사용
    double result = Operation.PLUS.apply();
    ```
    
    해당 코드는 새로운 상수를 추가하면 case 문도 추가해야한다는 단점이 있다.
    
2. 상수별 메서드 구현
    
    ```java
    public enum Operation {
        PLUS  { public double apply(double x, double y) { return x + y; }},
        MINUS { public double apply(double x, double y) { return x - y; }},
        TIMES { public double apply(double x, double y) { return x * y; }},
        DIVIDE { public double apply(double x, double y) { return x / y; }};
        
        public abstract double apply(double x, double y);
    }   
    ```
    
    apply()가 추상 메서드이기 때문에, 재정의하지 않을 경우 컴파일 오류가 발생한다.
    
    상수별 메서드 구현에 더해 상수별 데이터로 구현할 수도 있다.
    

1. 상수별 클래스 몸체와 데이터
    
    ```java
    public enum Operation {
    		// 상수별로 클래스 몸체를 가지고 있다.
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
    		
    		// 생성자
        Operation(String symbol) { this.symbol = symbol; }
    		
    		// toString메서드 재정의
        @Override public String toString() { return symbol; }
    
        public abstract double apply(double x, double y);
     }
    ```
    
    **사용자가 입력한 symbol에 따라 상수별 메서드를 수행**하는데 fromString메서드도 있다면 좋다.
    

### 열거 타입용 fromString 메서드

toString이 반환하는 문자열을 해당 열거타입의 상수로 변환해주는 메서드이다.

```java
// 문자열(symbol)과 열거형 상수를 매핑
public static final Map<String, Operation> stringToEnum =
	Stream.of(values()) .collect(
    	toMap(Object::toString, e -> e));

// 문자열(symbol)을 열거형 상수로 변환
public static Optional<Operation> fromString(String symbol){
	return Optional.ofNullable(stringToEnum.get(symbol));
}   
```

- `Stream.of(values())`
    - `Stream.of` : 배열을 스트림으로 변환
    - `values()` : 열거 타입의 내부 메서드로 열거 타입의 모든 상수 값을 배열로 반환
- `collect(toMap(Object::toString, e -> e))` : 스트림을 맵으로 변환
    - Object::toString : 열거형 상수의 symbol을 key로 사용
    - 열거형 상수를 value로 사용

- `stringToEnum.get(symbol)` : 매개변수로 들어온 symbol을 key값으로 value를 받아온다.
- `Optional.ofNullable` : null일 수도 있는 값을 감싸는 Optional 객체

### Optional

정교하게 코드를 작성하기위한 방법으로 null pointerExecption오류가 발생할 경우 **오류를 발생시키지 않고 프로그램을 안전하게 종료**하는 것

```java
Optional<BoardResponse> result = service.findBoard(BoardRequest.builder().idx(idx).build());

System.out.println("debug >> isPresent : " + result.isPresent());
System.out.println("debug >>> view result : " + result);

return new ResponseEntity<Optional<BoardResponse>>(result, HttpStatus.OK);
```

`service.findBoard(...)`의 반환값에 따라 Optional객체인 `result`의 값도 달라지게 된다.

- 반환값에 객체가 있다면 그 객체가 담기게 된다.
    
     ![](https://velog.velcdn.com/images/calla3494qhk/post/c7834d9e-e04f-446b-abdf-13b01b369950/image.png)
    

- 반환값이 없다면 Optional.empty가 담기게 된다.
    
    ![](https://velog.velcdn.com/images/calla3494qhk/post/1b41cf2d-3fe4-4891-964e-8d4abc5c8601/image.png)
    
    ![](https://velog.velcdn.com/images/calla3494qhk/post/f80dea3c-d23b-40e7-b319-a3d7b587c496/image.png)
    
    오류를 발생시키지 않고 null이라는 값으로 반환된다.
    
    → **그렇기에 메서드의 return type을 Optional로 지정해 Optional 객체를 반환하는 것이 좋다.**
    



> ⭐ Optional 메서드
> - `isPresent()` : 값이 있는지의 여부를 반환한다. <br>
    → 존재한다면 true, 없다면 false <br>
> - `get()` : 안에 객체가 있으면 반환하고 없으면 null을 반환해 오류가 발생한다.

## 전략별 열거 타입

열거 타입의 **상수 일부가 같은 동작을 공유**하는 경우 사용한다.

**열거 타입안에 또 다른 열거 타입**이 존재한다.

```java
enum PayrollDay{
	// 상수
	MONDAY(WEEKDAY), TUESDAY(WEEKDAY), WEDNESDAY(WEEKDAY),
    THURSDAY(WEEKDAY), FRIDAY(WEEKDAY),
    SATURDAY(WEEKEND), SUNDAY(WEEKEND);
    
    // 필드
    private final PayType payType;
    // 생성자 - payType을 입력받는다.
    PayrollDay(PayType payType) { this.payType = payType;}
    
    // 
    int pay(int minutesWorked, int payRate){
    	return payType.pay(minutesWorked,payRate);
    }
    
    //전략 열거 타입
    enum PayType{
    	WEEKDAY{
		    	// 상수별 메서드 구현
        	int overtimePay(){
                 return minsWorked <= MINS_PER_SHIFT ? 0 :
                 (minsWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND{
        	int overtimePay(){
              return minsWorked * payRate / 2;
            }
        };
        
        // 추상 메서드
        abstract int overtimePay(int mins, int payRate);
        private static final int MINS_PER_SHIFT =.  * 60;
        
        int pay(int minutesWorked, int payRate){
            int basePay = minsWorked * payRate;
            // 기본 급여 + 추가 수당
            return basePay + overtimePay(minsWorked, payRate);
        }
     }
}
```

## 핵심 정리

열거 타입은 정수 상수보다 더 읽기 쉽고 안전하고 강력하다. 

대다수 열거 타입이 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작할 때는 필요하다. 

드물게 하나의 메서드가 상수별로 다르게 동작해야 할 떄는, switch문 대신 상수별 메서드 구현을 사용하자. 

열거 타입 상수 일부가 같은 동작을 공유한다면, 전략 열거 타입 패턴을 사용하면 된다.