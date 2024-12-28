# 아이템 44 : 표준 함수형 인터페이스를 사용하라

# 함수형 매개변수 타입

- 템플릿 메서드 패턴 : **추상 클래스의 상속**을 통해 변화하는 부분을 각각 구현
    
    ```java
    abstract class Cook {
    	// 공통 메서드
        public void cooking() {
            System.out.println("음식 조리 시작");
            variant();
            System.out.println("음식 조리 완료");
        }
        
        // 변화하는 부분
        abstract void variant();
    }
    
    class Baking extends Cook {
    	@override
        public void variant() {
         	...
        }
    }
    ```
    

- 전략 패턴 : **변화하는 부분 or 메서드를 캡슐화**하고 이를 **매개변수로 받는** 메서드나 생성자를 제공
    
    → 이 전략 패턴에서 람다를 사용할 수 있다.
    
    ```java
    public class Cook {
         public void cooking(Strategy strategy) {
            System.out.println("음식 조리 시작");
            strategy.variant();
            System.out.println("음식 조리 완료");
        }
    }
    
    // 람다 사용
    Cook.cooking(() -> (System.out.println("라면 끓이는중");));
    ```
    

위의 **함수형 매개변수(strategy) 타입**은 직접 구현할 수 있지만 이미 만들어져 있는 **표준 함수형 인터페이스를 사용**하는 것이 좋다.


> ⭐ 용어 짚고가기
>  - **함수 객체** : 함수형 인터페이스를 구현한 클래스, 익명 클래스, 또는 람다 표현식
> - **함수형 매개변수 타입** : 특정 알고리즘이나 행동을 동적으로 변경할 수 있도록 하는 매개변수의 타입




# 함수형 인터페이스

하나의 추상 메서드만을 가지는 인터페이스

## 예시 - LinkedHashMap

`LinkedHashMap`의 `removeEldestEntry()` : Map에 원소가 100개 이상이 되면 true를 반환한다.

→ put()메서드에 의해 호출되며 put()메서드는 true가 반환되면 새로운 Key가 더해질 때마다 가장 오래된 원소를 하나씩 제거한다.

- 기존 방식
    
    ```java
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest){
    	return size() > 100;  //size : 호출된 맵 안의 value 수 반환
    }
    ```
    

- 함수형 인터페이스 방식
    
    ```java
    // 함수형 인터페이스 정의
    @FunctionalInterface interface EldestEntryRemovalFunction<K,V>{
    	boolean remove(Map<K,V> map, Map.Entry<K,V> eldest); 
    }
    
    // 활용
    // 함수 객체 생성
    EldestEntryRemovalFunction<String, Integer> removalFunction = (map, eldest) -> {
        return map.size() > 10; // 맵의 크기가 10을 초과하면 제거
    };
    
    // LinkedHashMap의 생성자에서 함수 객체를 사용
    LinkedHashMap<String, Integer> map = new LinkedHashMap<String, Integer>(16, 0.75f, true) {
        @Override
        protected boolean removeEldestEntry(Map.Entry<String, Integer> eldest) {
            // 생성자에서 함수 객체를 사용
            return removalFunction.remove(this, eldest);
        }
    };
    ```
    
    **함수 객체는 LinkedHaspMap의 인스턴스 메서드가 아니기 때문**에 LinkedHashMap의 size()메서드를 사용하지 못한다.
    
    → Map 객체를 함께 받아야 LinkedHashMap의 인스턴스 메서드를 사용할 수 있다.
    


> ⭐ **@FunctionalInterface** <br>
직접 만든 함수형 인터페이스는 해당 어노테이션이 반드시 존재해야 한다.
> 1. 해당 클래스의 코드나 문서를 읽을 이에게 **인터페이스가 람다용으로 설계된 것**임을 알려준다. <br>
    → 앞선 아이템에서 함수형 인터페이스를 람다로 구현하는 것의 장점을 소개했었다.   
> 2. 해당 인터페이스가 추상 메서드를 오직 하나만 가지고 있어야 컴파일 되게 해준다.
> 3. 유지보수 과정에서 누군가 실수로 메서드를 추가하지 못하게 막아준다.



## 표준 함수형 인터페이스 활용

위의 함수형 인터페이스와 같은 모양의 인터페이스가 이미 **자바 표준 라이브러리에 존재**한다.

```java
@FunctionalInterface
public interface BiPredicate<T, U> {
    boolean test(T t, U u);
}

// 활용
BiPredicate<Map<String, String>, Map.Entry<String, String>> predicate
     = (k, v) -> k.size() > 100;
```

# 표준 함수형 인터페이스 종류

표준 함수형 인터페이스는 총 43개이며, 기본 인터페이스 6개를 통해서 나머지 부분을 유추할 수 있다.

## Operator 인터페이스

- **UnaryOperator** : 반환값과 인수의 타입이 같은 함수를 뜻하며, 인수가 한개이다.
    
    → **입력된 값을 변환하여 같은 타입으로 반환**하는 함수
    
    ```java
    @FunctionalInterface
    public interface Function<T, R> {
        R apply(T t);
    }
    
    @FunctionalInterface
    public interface UnaryOperator<T> extends Function<T, T> {
    	 ...
    }
    
    // 예시: String::toLowerCase
    @Test
    void UnaryOperator_Test() {
       UnaryOperator<String> unaryOperator = String::toLowerCase;
       assertThat(unaryOperator.apply("ABC")).isEqualTo("abc");
    }
    ```
    

- **BinaryOperator** : 반환값과 인수의 타입이 같은 함수를 뜻하며, 인수가 두개이다.
    
    ```java
    // 선언
    @FunctionalInterface
    public interface BiFunction<T, U, R> {
        R apply(T t, U u);
    }
    
    @FunctionalInterface
    public interface BinaryOperator<T> extends BiFunction<T,T,T> {
       ...
    }
    
    // 예시: BigInteger::add
    @Test
    void BinaryOperator_Test() {
       BinaryOperator<BigInteger> binaryOperator = BigInteger::add;
       assertThat(binaryOperator.apply(BigInteger.ONE, BigInteger.TWO)).isEqualTo(BigInteger.valueOf(3L));
    }
    ```
    

### 굳이 Operator를 사용해야 하는 이유

```java
UnaryOperator<String> toLowerCase = String::toLowerCase;
UnaryOperator<String> addExclamation = s -> s + "!";

String result = addExclamation.apply(toLowerCase.apply("HELLO"));
System.out.println(result); // 출력: hello!
```

코드의 가독성을 높이고 함수의 의미를 명확하게 전달할 수 있다.

## **Predicate 인터페이스**

인수 하나를 받아 `boolean` 을 반환하는 함수
→ 인자로 주어진 매개변수가 함수의 조건에 맞다면 `true` 를 반환하고, 아니라면 `false` 를 반환한다.

→ **타입을 체크**할 때 사용하면 좋다.

```java
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
} 

// 예시: Collection::isEmpty
@Test
void Predicate_Test() {
   Predicate<Collection> predicate = Collection::isEmpty;
   assertThat(predicate.test(new ArrayList<>())).isTrue();
}
```

## Function 인터페이스

인수와 반환 타입이 다른 함수

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}

// 예시: Arrays::asList
@Test
void Function_Test() {
   Function<int[], List> function = Arrays::asList;
   int[] array = {1, 2};
   List<Integer> list = function.apply(array);
   assertThat(function.apply(array).size()).isEqualTo(2);
}
```

## Supplier 인터페이스

인수를 받지 않고 값을 반환(혹은 제공)하는 함수

```java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}

// 예시: Instant::now
@Test
void Supplier_Test() {
   Instant past = Instant.MIN;
   Supplier<Instant> supplier = Instant::now;
   assertThat(supplier.get().isAfter(past)).isTrue();
}
```

## **Consumer 인터페이스**

인수를 하나 받고 반환값은 없는(특히 인수를 소비하는) 함수

```java
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);  
}

// 예시: System.out::println
@Test
void Consumer_Test() {
   OutputStream output = new ByteArrayOutputStream();
   System.setOut(new PrintStream(output));
   Consumer consumer = System.out::print;
   consumer.accept("안녕");
   assertThat(output.toString()).isEqualTo("안녕");
}
```

## 커스텀 함수형 인터페이스

1. 표준 인터페이스 중 필요한 용도에 맞는 인터페이스가 없을 때
2. 구조적으로 똑같은 표준 함수형 인터페이스가 있지만 다음 조건을 따를 경우
    - API에서 자주 사용되며, 이름 자체가 용도를 명확히 설명해준다.
    - 구현하는 쪽에서 반드시 따라야 하는 규약이 있다.
    - 비교자들을 변환하고 조합해주는 유용한 디폴트 메서드를 제공할 수 있다.

### 예시 - Comparator<T>

```java
// ToIntBiFunction<T,U> 인터페이스
@FunctionalInterface
public interface ToIntBiFunction<T, U> {
    int applyAsInt(T t, U u);
}

// Comparator<T> 인터페이스
@FunctionalInterface
public interface Comparator<T> {
      
    // 반드시 따라야 하는 규약
    // Compares its two arguments for order. 
    // Returns a negative integer, zero, or a positive integer as 
    // the first argument is less than, equal to, or greater than the second.
    
    int compare(T o1, T o2);
    
    // 유용한 디폴트 메서드
    default Comparator<T> reversed() {
        return Collections.reverseOrder(this);
    }
}
```

구조적으로 ToIntBiFunction<T,U> 인터페이스와 동일하지만 아래의 이유로 따로 구현되었다.

1. 반드시 따라야 하는 규약
    - 정렬 순서를 기준으로 두 개의 인자를 비교한다.
    - 이 메서드는 아래의 값을 반환한다.
        - 음수 : 첫 번째 인자가 두 번째 인자보다 작을 때 반환
        - 0 : 첫 번째 인자가 두 번째 인자와 같을 때 반환
        - 양수 : 첫 번째 인자가 두 번째 인자보다 클 때 반환
2. 유용한 디폴트 메서드 : `reversed()`

## 함수형 인터페이스 주의사항

**서로 다른 함수형 인터페이스**를 같은 위치의 인수로 받는 메서드들을 다중 정의해서는 안 된다. 

→ 클라이언트에게 불필요한 모호함만 안겨줄 뿐이며, 실제로 문제가 일어나기도 한다.

```java
public interface ExecutorService extends Executor {
		// Callable과 Runnable 함수형 인터페이스를 다중 정의
    <T> Future<T> submit(Callable<T> task);
    Future<?> submit(Runnable task);
}
```

## 핵심 정리

입력값과 반환값에 **함수형 인터페이스 타입을 활용**하자. 

보통은 java.util.function 패키지의 **표준 함수형 인터페이스를 사용**하는 것이 가장 좋은 선택이지만, **직접 새로운 함수형 인터페이스**를 만들어 쓰는 편이 나을 수도 있다.