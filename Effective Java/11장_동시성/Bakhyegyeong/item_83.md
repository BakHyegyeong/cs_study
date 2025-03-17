# 아이템 83 : 지연 초기화는 신중히 사용하라

# 지연 초기화 **(lazy initialization)**

필드의 초기화 시점을 그 값이 처음 필요할 때까지 늦추는 기법이다.

→ 값이 쓰이지 않으면 초기화도 일어나지 않는다.

- 정적 필드와 인스턴스 필드 모두 적용 가능하다.
- 주로 최적화 용도로 쓰이지만, 클래스와 인스턴스 초기화 때 발생하는 위험한 순환 문제를 해결하는 효과도 있다.
- 실제 **필요한 시점에 객체를 생성**하므로 불필요한 부하를 줄인다.

## 지연 초기화의 문제점

- 클래스 혹은 인스턴스 생성 시 초기화 비용은 줄어들지만 지연 초기화하는 필드에 접근하는 비용은 커진다.
- 지연 초기화가 실제로는 성능을 느려지게 할 수도 있다.
    
    → 지연 초기화를 하는 필드 중 결국 초기화가 이뤄지는 비율, 실제 초기화에 드는 비용, 초기화된 각 필드를 얼마나 빈번히 호출하느냐에 따라 결정된다.
    
    → 해당 클래스의 인스턴스 중 **그 필드를 사용하는 인스턴스의 비율이 낮은 반면, 그 필드를 초기화하는 비용이 클 때** 사용하면 좋다.
    
- 멀티 스레드 환경에서는 사용하기 어렵다.
    
    → 지연 초기화하는 필드를 둘 이상의 스레드가 공유한다면 어떤 형태로든 반드시 동기화해야 한다.
    
- 대부분의 상황에서 일반적인 초기화가 지연 초기화보다 낫다.
- 테스트 관점에서 비효율적이다.

## 사용 예시

아래 코드는 인스턴스 필드를 **일반적인 초기화**로 수행한 것이다.

```java
private final FieldType field = computeFieldValue();
```

### **synchronized**

멀티스레드 환경에서 지연 초기화가 문제를 일으킬 수 있기 때문에 synchronized를 적용한다.

→ 2개의 스레드가 동시에 해당 필드에 접근할 경우 초기화를 2번 수행할 수 있다.

```java
private FieldType field;

private synchronized FieldType getField() {
  if (field == null)
    field = computeFieldValue();
  return field;
}
```


> ⭐ **초기화 순환성** <br>
> A클래스의 생성자가 B의 인스턴스를 생성 → B클래스의 생성자가 C의 인스턴스를 생성 → C클래스의 생성자가 A의 인스턴스를 생성하는 경우
> 
> 위의 경우에는 A객체 하나만 생성해도 StackOverFlow가 발생한다. 
>
>→ 각 클래스에 지연 초기화를 적용하면, 해당 객체가 사용될 때만 초기화 되므로 초기화 순환성을 깨뜨릴 수 있다.
>
>→ 다만 이 부분의 경우 클래스가 로딩되는 순서에 따라 초기화 순서가 불명확하기 때문에 적합한 설명은 아니라고 추측된다.



### **지연 초기화 홀더 클래스 관용구**

성능 때문에 **정적 필드를 지연 초기화해야 한다면** 지연 초기화 홀더 클래스 관용구를 사용한다.

→ 클래스가 처음 쓰일 때 생성자를 통해 초기화된다는 특성을 이용한 관용구이다.

```java
private static class FieldHolder {
  static final FieldType field = computeFieldValue();
}

private static FieldType getField() { 
	return FieldHolder.field; 
}
```

- getField가 처음 호출되는 순간 FieldHolder.field가 처음 읽히면서, FieldHolder 클래스가 초기화된다.
- getField 메서드가 필드에 접근하면서 동기화를 하지 않으니 성능이 느려지지 않는다.
- 일반적인 VM은 오직 클래스 초기화 단계에서만 필드 접근 동기화를 걸고 초기화 후에는 동기화 코드를 제거하여 아무런 검사나 동기화 없이 필드에 접근하게 된다.

### **이중검사 관용구**

성능 때문에 인스턴스 필드를 지연 초기화해야 한다면 이중검사 관용구를 사용한다.

→ 초기화된 필드에 접근할 때의 동기화 비용을 없애준다.

```java
private volatile FieldType field;

public FieldType getField() {
    FieldType result = field;
    if (result != null) // 첫 번째 검사 (락 사용 안 함)
        return result;
    
    synchronized(this) {
        if (field == null) // 두 번째 검사 (락 사용)
            field = computeFieldValue();
        return field;
    }
}
```

- 필드의 값을 두 번 검사하는 방식이다.
    - 첫 번째는 동기화 없이 검사한다.
        - 속도 향상을 목적으로 한다.
        - 초기화된 경우 동기화 없이 필드에 접근할 수 있다.
    - 두 번째는 동기화하여 검사한다.
        - Thread-safe 목적으로 한다.
        - Multi-thread 환경에서 동시 초기화하는 상황을 방지한다.
- 필드가 초기화된 후로는 동기화하지 않으므로 해당 필드는 반드시 volatile로 선언해야한다.
- result 지역 변수는 필드가 이미 초기화된 상황에서는 그 필드를 딱 한 번만 읽도록 보장하는 역할을 한다.

### 단일 검사 관용구

이중검사의 변종 관용구로 반복해서 초기화해도 상관없는 인스턴스 필드를 지연 초기화하는 경우 초기화가 중복해서 일어날 수 있다.

```java
private volatile FieldType field;

private FieldType getField() {
  FieldType result = field;
  if (result == null)
    field = result = computeFieldValue();
  return result;
}

private static FieldType computeFieldValue() {
  return new FieldType();
}
```

## 핵심 정리

- 대부분의 필드는 지연시키지 않고 바로 초기화해야 한다.
- 성능 때문에 혹은 위험한 초기화 순환을 막기 위해서 지연초기화를 쓴다면 올바르게 사용해야 한다.
- 인스턴스 필드에는 이중검사 관용구를, 정적 필드에는 지연 초기화 홀더 클래스 관용구를 사용하자.
- 반복해 초기화해도 괜찮은 인스턴스에는 단일검사 관용구도 고려해보자.