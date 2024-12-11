# 아이템 27 : 비검사 경고를 제거하라

# 비검사 경고

```java
Set<String> exaltation = new HashSet();
```

위의 코드에서는 `HashSet<String>();`이 아니기 때문에 비검사 경고가 발생한다. 

비검사 경고는 제일 쉽게 제거할 수 있으므로, 할 수 있는 한 모든 비검사 경고를 제거하는 것이 좋다.


> 💡 컴파일러 경고 : 비검사 경고, 비검사 메서드 호출 경고, 비검사 매개변수화 가변인수 타입 경고, 비검사 변환 경고 등

## 비검사 경고 제거 방법

### 다이아몬드 연산자

```java
Set<Lark> exaltation = new HashSet<>();
```

`<>` 연산자를 사용하면 **컴파일러가 올바른 실제 타입 매개변수를 추론**해주기 때문에 직접 매개변수를 명시하지 않아도 된다.

## 비검사 경고를 제거할 수 없는 경우

### **@SuppressWarnings("unchecked")**

경고를 제거할 수는 없지만, **타입이 안전하다고 확신**할 수 있다면 `@SuppressWarnings("unchecked")` 어노테이션을 통해 경고를 숨길 수 있다.

```java
public <T> T[] toArray(T[] a) {
    if (a.length < size){
        // 생성한 배열과 매개변수로 받은 배열이 모두 T[]로 같으므로
        // 올바른 형변환이다.
        @SuppressWarnings("unchecked") 
        T[] result = (T[]) Arrays.copyOf(elementData, size, a.getClass()); 
        return result
    }
}
```

`@SuppressWarnings` 어노테이션은 개별 지역변수 선언부터 클래스 전체까지 선언할 수 있다.

하지만 항상 **가능한 좁은 범위에 적용**해야 하고 경고를 **무시해도 안전한 이유를 주석으로** 남겨야 한다.

## 핵심 정리

- 비검사 경고는 런타임에 `@ClassCastException`을 일으킬 수 있는 잠재적 가능성을 의미하니 가능한한 제거하자.
- 만약, 경고를 제거하기 힘들다면 타입 안정성을 증명하고 `@SuppressWarnings("unchecked")` 애너테이션으로 경고를 숨기고 그 근거를 주석으로 남기자.