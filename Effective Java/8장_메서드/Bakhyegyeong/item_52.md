# 아이템 52 : 다중정의는 신중히 사용하라

# 재정의 (Override)

상위 클래스가 정의한 것과 같은 시그니처의 메서드를 **하위 클래스에서 다시 정의한 것**

```java
class Wine {
    String name() { return "포도주"; }
}

// 하위 클래스에서 재정의
class SparklingWine extends Wine {
    @Override String name() { return "스파클링 와인"; }
}

class Champagne extends SparklingWine {
    @Override String name() { return "샴페인"; }
}

public static void main(String[] args) {
    List<Wine> wineList = List.of(
            new Wine(), new SparklingWine(), new Champagne());

    for (Wine wine : wineList)
        System.out.println(wine.name());  // "포도주", "발포성 포도주", "샴페인"
}
```

for문에서의 컴파일 타입은 모두 `Wine`이지만 **최하위에서 재정의한 메서드가 실행**된다. <br>
→ **컴파일 타임의 인스턴스 타입은 신경쓰지 않는다.**

# 다중정의(Overloading)

리턴 타입과 메서드 네이밍은 같지만 **파라미터의 개수나 타입이 다른 메서드**를 작성하는 것

→ 객체의 런타임 타입은 전혀 중요하지 않고 오직 **컴파일 타임에 매개변수의 컴파일 타입이 중요**하다.

```java
public class CollectionClassifier {
    public static String classify(Set<?> s) {
        return "집합";
    }

    public static String classify(List<?> lst) {
        return "리스트";
    }

    public static String classify(Collection<?> c) {
        return "그 외";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<BigInteger>(),
                new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c)); // "그 외", "그 외", "그 외"
    }
}
```

for 문 안의 **c는 항상 `Collection<?>` 타입이기 때문에 `classify(Collection<?> c)` 만 호출**된다.

```java
public class FixedCollectionClassifier {
    public static String classify(Collection<?> c) {
        return c instanceof Set  ? "집합" :
                c instanceof List ? "리스트" : "그 외";
    }
    ...
}
```

올바른 결과를 원한다면 **`instanceof`로 매개변수를 명시적으로 검사**해야 한다.


> ⭐ **재정의한 메서드는 동적으로 선택되고, 다중정의한 메서드는 정적으로 선택된다.** 


## 올바른 다중정의 사용법

사용자가 파라미터를 넘기면서 어떤 오버로딩 메서드가 호출될 지 모른다면 오류가 발생할 확률이 높다.

1. 다중정의가 혼란을 일으킨다면 사용하지 말아야 한다.
2. 매개변수 수가 같은 다중정의는 만들지 말아야 한다.
    
    → 가변인수(varags)를 사용하는 메서드라면 다중정의를 아예 하지 말아야 한다.
    
3. 다중정의 대신 메서드 이름을 다르게 한다.
    
    → `ObjectOutputStream` 클래스의 경우`writeBoolean()`, `writeInt()`와 같은 이름의 메서드를 제공한다.
    

## 다중정의 주의사항

### 생성자와 다중정의

생성자는 이름을 다르게 지을 수 없으니 두번째 생성자부터는 다중정의가 된다.

→ 파라미터 중 한 개 이상이 **서로 캐스팅할 수 없는 다른 타입**이면 안전하다.

→ **정적팩터리**를 사용해 생성 의도와 코드를 명확하게 할 수 있다.


> ⭐ 근본적으로 다르다 = **서로 캐스팅할 수 없다.**



### 오토박싱과 다중정의

오토박싱이 도입되고, 기본 타입과 참조 타입이 근본적으로 다름을 보장할 수 없게 되면서 문제가 발생했다.

```java
public class SetList {
    public static void main(String[] args) {
        Set<Integer> set = new TreeSet<>();
        List<Integer> list = new ArrayList<>();

		// -3 ~ 2까지 숫자를 add
        for (int i = -3; i < 3; i++) {
            set.add(i);
            list.add(i);
        }
        
        // 0 ~ 2까지의 숫자를 remove
        for (int i = 0; i < 3; i++) {
            set.remove(i);
            list.remove(i);
        }
        
        System.out.println(set + " " + list); 
        // [-3, -2, -1] [-2, 0, 2] 
    }
}
```

`set`의 결과는 옳게 나왔지만 **`list`의 결과는 다르게 나왔다.**

→ `List<E>` 인터페이스가 `remove(Object element)`와 `remove(int index)` **두가지 메서드를 다중정의** 했기 때문이다.

→ `set.remove(i)` 는 **`remove(Object element)` 외에 다중정의된 메서드가 없어** 원하는 결과가 나왔다.

→ `list.remove(i)` 는 **`remove(int index)` 가 실행돼 다른 결과**가 나왔다.

```java
for(int i = 0; i < 3; i++){
    set.remove(i);
    list.remove((Integer) i); 
    // 혹은 list.remove(Integer.valueOf(i))
} 
```

`Integer` 라는 박싱타입으로 캐스팅하여 정상동작하도록 변경했다.

### 람다와 메서드 참조와 다중정의

```java
// 1. Thread의 생성자 호출
new Thread(System.out::println).start();

// 2. ExecutorService의 submit 메서드 호출
ExecutorService exec = Executors.newCachedThreadPool();
exec.submit(System.out::println); // 컴파일 에러
```

Thread의 생성자와 ExecutorService의 submit 메서드 모두 함수형 인터페이스인 `Runnable`을 파라미터로 받는다.

→ submit 메서드의 경우 `Runnable` 뿐만 아니라 `Callable`도 받는 **다중정의된 메서드**이다.

→ 서로 다른 함수형 인터페이스라도 **인수 위치가 같으면 혼란이 생긴다.**

→ **달라 보이는 함수형 인터페이스도 근본적으로 다르지 않기 때문**에 컴파일러가 어떤 것을 선택해야 할지 알 수 없어 오류가 생긴다.

## 다중정의 사용 해결법 - 인수 포워딩

```java
public boolean contentEquals(StringBuffer sb) {
    return contentEquals((CharSequence)sb);
}
```

명시적 캐스팅으로 인수를 포워딩하여 **두 메서드가 동일한 일을 하도록 보장**한다.

## 핵심 정리

일반적으로 매개변수 수가 같을 때는 다중정의를 피하는것이 좋다. 

하지만 생성자와 같이 상황에 따라 조언을 따르기 불가능할때는 헷갈릴 만한 매개변수는 형변환하여 정확한 다중정의 메서드가 선택되도록 해야 한다. 

이것 또한 불가능하다면, 기존 클래스를 수정해 새로운 인터페이스를 구현해야 할때는 같은 객체를 입력받는 다중정의 메서드들이 모두 동일하게 동작하도록 만들어야 한다.