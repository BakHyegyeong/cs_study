# 아이템 32 : 제네릭과 가변인수를 함께 쓸 때는 신중하라

# 가변인수 메서드

메서드에 넘기는 인수의 개수를 클라이언트가 조절할 수 있는 메서드

```java
void sum(String...str) {  // ... : 가변인자
	for(String a:str)
    	System.out.println(a);
}
```

가변인수 메서드를 호출하면 **가변인수를 담기 위한 배열**이 자동으로 만들어진다.

→ **제네릭과 가변인수를 함께 사용하게 되면 제네릭 배열**이 생성되고, **힙 오염**이 발생하며 컴파일 경고가 발생한다.

## 힙 오염

**매개변수화 타입의 변수가 타입이 다른 객체를 참조**하면, 힙 오염이 발생한다.

```java
public class Dangerous {
    static void dangerous(List<String>... stringLists) {   //가변인수 메서드
        List<Integer> intList = List.of(42);
        Object[] objects = stringLists;
        objects[0] = intList; // 힙 오염 발생
        String s = stringLists[0].get(0); // ClassCastException
    }
}
```

마지막 줄에서 **컴파일러가 임의로 형변환**을 진행하면서 컴파일은 통과하지만 **다른 타입이므로 `ClassCastException`오류가 발생**한다.

### 제네릭 가변인자 매개변수의 모순점

제네릭 배열을 직접 선언하는 것은 허용하지 않으면서, **제네릭 가변인자 매개변수를 받는 메서드의 선언은 허용하는 것은 모순적**이다.

→ 실무에서 이 방법이 유용하기에 허용한다.

```java
Arrays.asList(T... a),
Collections.addAll(Collection<? super T> c, T... elements)
Enumset.of(E first, E...rest)
```

## @SafeVarags

본래에는 제네릭 가변인수 메서드를 사용함으로써 발생하는 경고에 대해 호출하는 곳 마다 **`@SuppresssWarnings("unchecked")` 를 달아 숨겨야 한다는 단점**이 있었다.

→ 자바 7 이후부터 `@SafeVarargs` 을 통해, **메서드 작성자가 그 메서드가 타입 안전한지 보장**할 수 있게 되었다.


> ⭐
`@SafeVarargs` 애너테이션은 재정의할 수 없는 메서드에만 달아야 한다. <br>
→ 자바 8에서는 오직 정적 메서드와 `final` 인스턴스 메서드에만 붙일 수 있다. <br>
→ 자바 9부터는 `private`인스턴스 메서드에도 허용된다.


### 메서드 안전성을 판단하는 방법

1. 메서드가 가변인자를 담는 **배열에 아무것도 저장하지 않는다.**
2. 가변인자를 담은 배열 혹은 복제본의 참조가 **밖으로 노출되지 않는다.**
→ 신뢰할 수 없는 코드가 배열에 접근하지 못하도록 해야 한다.

**가변인자 매개변수 배열이 순수하게 인자를 전달하는 역할**만 한다면 메서드가 안전하다고 판단한다.

### 예외사항

메서드가 가변인자를 담는 **배열에 아무것도 저장하지 않아도 다른 메서드가 접근하도록 허용**하는것 자체만으로도 **힙 오염이 발생할 수 있다.**

```java
public class PickTwo {
    static <T> T[] toArray(T... args) {  // 자신의 제네릭 매개변수 배열의 참조를 노출
        return args;
    }

    static <T> T[] pickTwo(T a, T b, T c) {
        switch(ThreadLocalRandom.current().nextInt(3)) {
		    // object 배열을 반환
            case 0: return toArray(a, b);
            case 1: return toArray(a, c);
            case 2: return toArray(b, c);
        }
        throw new AssertionError(); 
    }

    public static void main(String[] args) { 
		// 반환된 배열이 String[]으로 형변환
        String[] attributes = pickTwo("좋은", "빠른", "저렴한");
        System.out.println(Arrays.toString(attributes));
    }
}
```

1. `pickTwo` 메서드는 어떤 타입의 객체를 넘기더라도 담을 수 있는 가장 구체적인 타입의 **Object타입의 배열을 반환**한다.
2. 반환된 Object타입의 배열은 String[]으로 자동으로 형변환된다.
3. **`Object[]` 는 `String[]` 의 하위 타입이 아니므로** **형변환이 불가능하고, `ClassCastException` 이 발생**한다.

따라서, **`@SafeVaragrs`가 붙어있는 곳**이나 가변인자를 인자로 받지 않는 **일반 메서드에 배열을 넘기는 것**이 안전하다.

## **제네릭 가변인자 매개변수의 대안**

가변인자 매개변수를 **List 매개변수**로 바꿀 수 있다.

```java
static <T> List<T> pickTwo(T a, T b, T c){
    switch (ThreadLocalRandom.current().nextInt(3)){
		// 반환타입이 List.of
        case 0: return List.of(a,b);
        case 1: return List.of(b,c);
        case 2: return List.of(a,c);
    }
    
    throw new AssertionError(); // 도달 못함.
}
...
public static void main(String[] args) {
    final List<String> attributes = pickTwo("좋은", "빠른", "저렴한");
}
```

**장점**

1. 컴파일러가 메서드의 타입 안정성을 검증해준다.
2. 어노테이션을 직접 달 필요가 없다.
3. 사용자의 실수로 안전하지 않은데 안전하다고 판단할 일이 없다.

**단점**

1. 클라이언트 코드가 지저분해진다.
2. 속도가 배열에 비해 느리다.

## 핵심정리

가변인수와 제네릭은 궁합이 좋지 않다. 가변인수 기능은 배열을 노출하여 추상화가 완벽하지 못하고, 배열과 제네릭의 타입 규칙이 서로 다르기 때문이다. 

제네릭 가변인자 매개변수는 타입 안전하지는 않지만, 허용된다. 

메서드에 제네릭(혹은 매개변수화된) 가변인자 매개변수를 사용하고자 한다면, 그 메서드가 타입 안전한지 확인한 다음 @SafeVarargs 애너테이션을 달아 사용하는 데 불편함이 없게끔 하자.