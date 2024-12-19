# 아이템 37 : ordinal 인덱싱 대신 EnumMap을 사용하라

# ordinal()

**배열이나 리스트에서 원소를 꺼낼 때와 같이 인덱싱이 필요할때**는, `ordinal()` 메서드로 인덱스를 얻을 수 있다

- Plant 클래스 안에 생애 주기 상수 열거 타입이 있다.
    
    → 각 식물은 lifeCycle 상수를 가지고 있다.
    

```java
class Plant {
    enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }  // 생애주기

    final String name;
    final LifeCycle lifeCycle;

    Plant(String name, LifeCycle lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override public String toString() {
        return name;
    }
 }
```
<br>

- 생애주기의 **ordinal 메서드의 값을 배열의 index로** 사용하고자 한다.

```java
 // Set<Plant> 타입의 배열로 생애주기별로 식물을 저장
 Set<Plant>[] plantsByLifeCycleArr =
                (Set<Plant>[]) new Set[Plant.LifeCycle.values().length];
          
 // 배열의 각 요소를 초기화
 for (int i = 0; i < plantsByLifeCycleArr.length; i++)
        plantsByLifeCycleArr[i] = new HashSet<>();
 
 // garden : 식물 객체들이 들어있는 컬렉션
 for (Plant p : garden)
	// 해당 식물의 생애 주기의 인덱스를 배열의 인덱스로 사용
	// 해당 배열의 인덱스 안의 set에 식물 객체 추가
    plantsByLifeCycleArr[p.lifeCycle.ordinal()].add(p); // ordinal()사용 
       
 // 결과 출력
 for (int i = 0; i < plantsByLifeCycleArr.length; i++) {
      System.out.printf("%s: %s%n",
                    Plant.LifeCycle.values()[i], plantsByLifeCycleArr[i]);
 }
```

- 배열은 제네릭과 호환되지 않으니 비검사 형변환을 수행해야 한다.
- 정확한 정수값을 사용한다는 것을 직접 보증해야 한다.
    
    → 정수는 타입 안전하지 않다.
    

## EnumMap

**열거 타입을 키로 가지며**, **배열과 마찬가지로 실질적으로 열거 타입 상수를 값으로 매핑**한다.

```java
Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle =
                new EnumMap<>(Plant.LifeCycle.class);  // EnumMap
                  
for (Plant.LifeCycle lc : Plant.LifeCycle.values())
	// 열거 타입 상수를 key로 set을 value로 한다.
    plantsByLifeCycle.put(lc, new HashSet<>());

for (Plant p : garden)
	// 열거 타입 상수를 key로 value인 set을 가져온 후 식물 객체를 추가
    plantsByLifeCycle.get(p.lifeCycle).add(p);
      
System.out.println(plantsByLifeCycle);
```

- 안전하지 않은 형변환을 쓰지 않는다.
- 맵의 key인 열거 타입이 그 자체로 출력용 문자열을 제공하니 출력 결과에 직접 레이블을 달 필요 없다.
- EnumMap 내부 구현은 배열 방식이므로 성능이 동일하다.

## EnumMap과 Stream 활용

garden에 있는 식물들을 생애 주기별로 그룹화하여 각 생애 주기별로 식물들의 set을 만드는 예제이다.

```java
System.out.println(Arrays.stream(garden)
                .collect(groupingBy(p -> p.lifeCycle,
                        () -> new EnumMap<>(LifeCycle.class), toSet())));
}
```

- `Arrays.stream(garden)` : graden의 요소를 가진 stream 생성
- `collect` : 반환된 stream의 요소를 수집하여 결과를 생성
- `groupingBy` : 각 식물 p의 생애 주기를 기준으로 그룹화하여 같은 생애 주기를 가진 식물들은 Set으로 묶여 저장된다.
    
    → 그룹화의 결과를 EnumMap으로 저장한다.
    
    → **key는 LifeCycle의 상수, value는 toSet() 메서드를 통해 set으로 저장**된다.
    

스트림 버전에서는 **해당 생애주기에 속하는 식물이 있을 때만** 만들고, EnumMap 버전은 언제나 **생애주기당 하나씩 중첩 맵**을 만든다는 점에서 차이가 존재한다.

## 중첩 EnumMap

두 가지 상태(Phase)를 전이(Transition)와 맵핑하는 예제

→ LIQUID에서 SOLID의 전이는 FREEZE가 되고, LIQUID에서 GAS로의 전이는 BOIL이 될것이다.

```java
public enum Phase {
    SOLID, LIQUID, GAS;
    public enum Transition {
        MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;
        
        private static final Transition[][] TRANSITIONS = {
            {null, MELT, SUBLIME },
            {FREEZE, null, BOIL},
            {DEPOSIT, CONDENSE, null}
       };

       // 한 상태에서 다른 상태로의 전이를 반환
       public static Transition from(Phase from, Phase to){
	        return TRANSITIONS[from.ordinal()][to.ordinal()];
	        // ordinal 메서드를 통해 다차원 배열의 index를 표현
       }
   }
}
```

Phase나 Transition의 상수의 **선언 순서를 변경**하거나 **새로운 Phase 상수를 추가하는 경우**에도 문제가 발생한다.

### 중첩 EnumMap으로 해결

```java
public enum Phase {
    SOLID, LIQUID, GAS;
    
    public enum Transition {
		    // 각 전이에 맞는 이전 상태와 이후 상태를 필드로 가지고 있는 것으로 수정
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID),
        BOIL(LIQUID, GAS), CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID);
        
        private final Phase from;
        private final Phase to;
        
        Transition(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        private static final Map<Phase, Map<Phase, Transition>>
					      // key : 전이 이전 상태
                m = Stream.of(values()).collect(groupingBy(t -> t.from,
                () -> new EnumMap<>(Phase.class),
                // value : 전이 이후 상태를 key로 한 새로운 Map
                toMap(t -> t.to, t -> t,
				                // 만약 Key 값이 같은게 존재할때 Value를 기존값(x)으로 할지 새로운값(y)으로 갱신할지 정하는 부분
				                // value : 전이 이전과 이후 값을 기준으로 Transition을 명시
                        (x, y) -> y, () -> new EnumMap<>(Phase.class))));
        
        public static Transition from(Phase from, Phase to) {
            return m.get(from).get(to);
        }
    }
}
```

```java
// 결과값
{
    SOLID -> {
        LIQUID -> MELT,
        GAS -> SUBLIME
    },
    LIQUID -> {
        SOLID -> FREEZE,
        GAS -> BOIL
    },
    GAS -> {
        LIQUID -> CONDENSE,
        SOLID -> DEPOSIT
    }
}

// 사용
Transition transition = Transition.from(Phase.SOLID, Phase.LIQUID);
System.out.println(transition); // MELT

```

만약에 새로운 상태를 추가한다면, 배열로 만든 코드는 새로운 상수 추가 + 배열 크기 교체가 필요하다.

EnumMap 버전에서는 **상태 목록** (*ex)PLASMA*) 과 **전이 목록**(*ex) IONIZE(GAS, PLASMA), DEIONIZE(PLASMA, GAS)*) 에 **원소만 추가해주면 끝**이기 때문에 **간단하다는 장점**이 있다.

+) [코드 로직 정리](https://javabom.tistory.com/51)

## 핵심 정리

배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니, 대신 EnumMap을 사용하라. 

다차원 관계는 `EnumMap<..., EnumMap<...>>` 으로 표현하라.

+) [EnumMap과 HashMap 비교](https://github.com/woowacourse-study/2022-effective-java/blob/main/06%EC%9E%A5/%EC%95%84%EC%9D%B4%ED%85%9C_37/ordinal_%EC%9D%B8%EB%8D%B1%EC%8B%B1_%EB%8C%80%EC%8B%A0_EnumMap%EC%9D%84_%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC.md)