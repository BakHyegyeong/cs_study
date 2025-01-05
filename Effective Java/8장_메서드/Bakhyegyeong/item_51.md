# 아이템 51 : 메서드 시그니처를 신중히 설계하라

# 시그니처

메서드의 **메서드 명 + 파라미터의 순서, 타입, 개수**

→ 리턴 타입과 Exceptions는 포함하지 않는다.

```java
// 서로 다른 메서드 시그니처들
public void method() { } 
public void method2() { } 
public void method(int a) { } 
public void method(String s) { }
public void method(int a, int b) { }

// 서로 같은 메서드 시그니처
public void method() { } 
public int method() { } 
public String method() throws Exception { } 
```

# API 핵심 설계 요령

## 메서드 이름은 신중히 짓자

- **표준 명명 규칙**을 따라야 한다.
    - 메서드 이름은 lowerCamelCase로 작성한다.
    - 메서드 이름은 동사/전치사로 시작한다.
    - 예약어를 사용해서는 안 된다. (ex. do, while..)
- **이해할 수 있고** 같은 패키지에 속한 다른 이름들과 **일관되게** 지어야 한다.
- 개발자 커뮤니티에서 **널리 받아들여지는 이름**을 사용해야 한다.
- 너무 긴 이름은 피하는 것이 좋다.

### 메서드 이름으로 자주 사용되는 동사/전치사

- get/set()
- init : 데이터 초기화
- is/has/can : boolean 값 리턴
- create : 새로운 객체 생성 후 리턴
- find : 데이터 찾기
- to : 다른 형태의 객체로 반환
- by

### **참고하면 좋은 네이밍시 중요한 고려사항**

- 왜 존재해야 하는가
- 무슨 작업을 하는가
- 어떻게 사용하는가

## 편의 메서드를 너무 많이 만들지 말자

없어도 기능적으로 문제가 없지만 **편의를 위해서 존재**하는 메서드

- 자주 사용하는 메서드를 여러개 묶어서 새로운 메서드를 생성
- 메서드에 이름을 부여하여 좀 더 명확하게 무슨 일을 하는지 표현

```java
// 자주 사용하는 메서드를 여러개 묶어서 새로운 메서드를 생성
private void validateNumber() {
    validateEmpty();
    validateNegaive();
    validateRange();
    validateDuplication();
}

// 메서드에 이름을 부여하여 좀 더 명확하게 무슨 일을 하는지 표현
private static boolean isSecondRank(int matchCount, boolean matchBonusNumber) {
    return matchCount == Rank.SECOND.matchCount && matchBonusNumber;
}
```

메서드가 너무 많은 클래스와 인터페이스는 **익히고, 사용하고, 문서화하고, 테스트하고, 유지보수하기가 어렵다.**

→ 클래스나 인터페이스는 자**신의 각 기능을 완벽히 수행하는 메서드로만 제공**하고, 확신히 서지 않는다면 만들지 말자.

## 매개변수 목록은 짧게 유지하자

**4개 이하가 적정**이며, 그 이상은 매개변수들을 기억하기가 쉽지 않아진다. 

→ 같은 타입의 매개변수 여러 개가 연달아 나오는 경우는 특히나 안좋다.

### 긴 매개변수 목록을 짧게 줄이는 방법

1. **여러 메서드로 분리** : 메서드가 많아질 수 있지만 **직교성을 높여** 메서드 수를 줄여주는 효과도 있기 때문에 괜찮다.
    
    
    ex) List : 지정된 범위의 부분리스트에서 주어진 원소의 인덱스를 찾아야 한다고 가정
    
    → (**부분리스트 시작/끝/찾을 원소)**로 총 3개의 매개변수가 필요하다.
    
    → `subList()` : 부분 리스트를 반환
    
    → `indexOf()` : 주어진 원소의 인덱스를 반환
    
    → 이 둘을 조합해 사용할 수 있다.
    
    > ⭐  
    >  
    > **직교성**: 공통점이 없는 기능들이 잘 분리되어 있는 것  
    >  
    > → 기능을 원자적으로 쪼개다 보면, **기본 기능만 가지고도 여러 복잡한 기능들을 조합**해낼 수 있기 때문에 불필요한 메서드를 만들지 않아도 된다.  

    

1. **도우미 클래스 생성** : 하나의 개념으로 묶일 수 있는 정적 멤버 클래스를 두고 있는 클래스를 생성한다.
    
    
    ```java
    //Bad Case
    public void giveCard(String suit, int number, int height, int width) {}
        
    //Good Case
    class Blackjack {
        // 도우미 클래스 (정적 멤버 클래스)
        static class Card {
            private String suit;
            private int number;
            private int height;
            private int width;
        }
        public void giveCard(Card card);
    }
    ```
    

1. **객체 생성에 사용한 빌더 패턴을 메서드 호출에 응용** : 앞서 두 방식을 혼합한 것
    
    
    ```java
    // 설정할 매개변수를 담는 클래스
    class Configuration {
        private String setting1;
        private int setting2;
        private boolean setting3;
    
        // 세터 메서드
        public Configuration setSetting1(String setting1) {
            this.setting1 = setting1;
            return this; 
            // 메서드 체이닝을 위해 자기 자신을 반환 또는 @Builder 어노테이션 사용
        }
        public Configuration setSetting2(int setting2) {
            this.setting2 = setting2;
            return this; 
        }
        public Configuration setSetting3(boolean setting3) {
            this.setting3 = setting3;
            return this; 
        }
    
        // 유효성 검사 메서드
        public void validate() {
            ...
        }
    
        // 유효성 검사 후 작업 수행
        public void execute() {
            validate();
            ...
        }
    }
    
    // 클라이언트 코드
    public class Main {
        public static void main(String[] args) {
            Configuration config = new Configuration()
                .setSetting1("example")
                .setSetting2(10)
                .setSetting3(true);
    
            // 설정이 완료되면 execute 메서드 호출
            config.execute();
        }
    }
    ```
    

## 매개변수 타입으로는 클래스보다는 인터페이스가 더 낫다.

매개변수로 적합한 인터페이스가 있다면, 구현한 클래스가 아닌 인터페이스를 직접 사용하는 것이 좋다. 

→ 클래스를 사용하면 **클라이언트에게 특정 구현체만 사용하도록 제한**하는 것이 되기 때문이다.

```java
//Bad Case
public Statistics createStatistics(HashMap<Rank, Integer> result) {}
createStatistics(new HashMap<>());

//Good Case
public Statistics createStatistics(Map<Rank, Integer> result) {}
createStatistics(new HashMap<>());
createStatistics(new LinkedHashMap<>());
createStatistics(new TreeMap<>());
```

## boolean 보다는 열거 타입이 낫다.

열거 타입을 사용하면 코드를 읽고 쓰기가 더 쉬워지며, 나중에 선택지를 추가하기도 쉽다.

→ 메서드 이름상 boolean을 받아야 의미가 더 명확할 때는 제외한다.

```java
//Bad Case
public LottoTicket publishLotto(boolean isAuto) {}

//Good Case
public enum LottoType{
    Auto,
    Manual;
}
public LottoTicket publishLotto(LottoType lottoType) {}
```

```java
public enum TemperatureSacle { FAHRENHEIT, CELSIUS }
```

`Thermometer.newInstance(true)` 보다는 
`Thermometer.newInstance(TemperatureScale.CELSIUS)` 가 의미가 더 명확하다.

→ 이후에 종류가 늘어나면 정적 메서드를 추가할 필요 없이 열거 타입에만 추가해주면 된다.