# 아이템 7 : 다 쓴 객체 참조를 해제하라
C, C++처럼 메모리를 직접 관리해야하는 언어와 달리 자바는 가비지 컬렉터가 있지만 메모리 관리에 신경을 안써도 되는 것은 아니다.

## 메모리 누수 예시 - Stack

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }

    /**
     * 원소를 위한 공간을 적어도 하나 이상 확보한다.
     * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
     */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

}
```

스택이 커졌다가 줄어들었을 때 **스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다.** 

→ 이 스택이 그 객체들의 다 쓴 참조(obsolete reference)를 여전히 갖고 있기 때문이다.

→ **객체 참조 하나를 살려두면 가비지 컬렉터는 그 객체뿐 아니라 그 객체를 참조하는 모든 객체(그리고 또 그 객체들이 참조하는 모든 객체 등)를 회수해가지 못한다.**

<aside>
💡

다 쓴 참조 : 앞으로 다시 쓰지 않을 참조

</aside>

### 해결 방법

```java
public class Stack {
	...

    // 코드 7-2 제대로 구현한 pop 메서드 (37쪽)
    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }
}
```

하지만 **객체 참조를 null 처리하는 일은 예외적인 경우여야 한다.** 

다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것이다. 

이 변수의 범위를 최소가 되게 정의(아이템 57)했으면 자연스럽게 이뤄진다.

```java
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    
    // 지역 변수로 객체 참조 가져오기
    Object result = elements[size - 1];
    
    // 참조를 해제
    elements[size - 1] = null;
    size--;
    
    // 결과를 반환
    return result;
}
```

https://sharonprogress.tistory.com/232

### 메모리 직접 관리

가비지 컬렉터는 stack클래스 내부의 배열의 활성 영역과 비활성 영역을 알 수 없으므로 이를 직접 인식시켜주어야 한다.

→ 모든 객체를 null로 처리해줄 필요는 없고, **메모리를 직접 관리하여 가비지 컬렉터가 불필요한 객체임을 인지하지 못하는 경우 null로 선언**하면 된다.

## 메모리 누수 예시 - 캐시

```java
Object key1 = new Object();
Object value1 = new Object();

Map<Object, List> cache = new HashMap<>();
cache.put(key1, value1);
```

key1이 없어지면 이 캐싱 자체가 무의미해지는 경우

### 해결 방법

WeakHashMap을 통해 구현하면 다 쓴 엔트리는 자동으로 제거된다.

```java
Object key1 = new Object();
Object value1 = new Object();

Map<Object, List> cache = new WeakHashMap<>();
cache.put(key1, value1);
```

### WeakHashMap

: 기본적으로 HashMap과 유사하지만 **키에 대한 참조가 약한참조(weak reference)로 저장**되는 것이 특징이다.

 → 일반적인 참조와 달리 **가비지 컬렉터에 의해 언제든지 회수**될 수 있다.

<aside>
💡

약한 참조 : WeakHashMap의 **키로 사용되는 객체에 대한 유일한 참조가  WeakHashMap 내부에만 있는 것**

**→ value를 얻기 위해 사용되는 것 말고는 사용되지 않는 것** 

</aside>

```java
import java.util.WeakHashMap;

public class Example {
    public static void main(String[] args) {
        WeakHashMap<Object, String> weakMap = new WeakHashMap<>();

        Object key = new Object();
        weakMap.put(key, "value");

        System.out.println("Before nulling key: " + weakMap);

        key = null; // key에 대한 strong reference를 제거

        System.gc(); // 가비지 컬렉터 호출

        System.out.println("After GC: " + weakMap); // map 제거
    }
}
```

## 메모리 누수 예시 - 리스너 또는 콜백

클라이언트가 콜백을 등록만 하고 명확히 해지하지 않는다면, 뭔가 조치해주지 않는 한 콜백은 계속 쌓여갈 것이다.

→ 이럴 때 **콜백을 약한 참조(weak reference)**로 저장하면 가비지 컬렉터가 즉시 수거해간다. (ex. WeakHashMap에 키로 저장하면 된다.)

```java
import java.util.WeakHashMap;

interface EventListener {
    void onEvent(String event);
}

class EventManager {
    // 리스너를 WeakHashMap의 키로 저장하여 약한 참조를 유지
    private WeakHashMap<EventListener, Boolean> listeners = new WeakHashMap<>();

    public void registerListener(EventListener listener) {
        listeners.put(listener, Boolean.TRUE);
    }

    public void fireEvent(String event) {
        for (EventListener listener : listeners.keySet()) {
            listener.onEvent(event);
        }
    }
}

public class Example {
    public static void main(String[] args) {
        EventManager manager = new EventManager();

        // 리스너를 등록
        EventListener listener = event -> System.out.println("Received event: " + event);
        manager.registerListener(listener);

        // 이벤트 발생
        manager.fireEvent("Test Event 1");

        // 리스너 참조를 제거
        listener = null;

        // 가비지 컬렉터를 힌트로 실행
        System.gc();

        // 이벤트 발생
        manager.fireEvent("Test Event 2"); // 이전 리스너는 더 이상 출력되지 않는다.
    }
}
```
