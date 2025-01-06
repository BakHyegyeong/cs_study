# 아이템 53 : 가변인수는 신중히 사용하라

# 가변 인수 메서드

명시한 타입의 인수를 **0개 이상 받을 수 있는 메서드**

→ 매개변수의 수가 정해져 있지 않는 메서드

```java
static int min(int firstArg, int ... remainingArgs) {
    int min = firstArg;
      
    for (int arg : args) {
        if (min > arg) {
           min = arg;
        }
	}
              
    return min;
}
```

**인수의 개수와 길이가 같은 배열을 따로 만들고 인수들을 배열에 저장**하여 가변인수 메서드에 전달한다.

## 가변인수 주의사항

메서드 호출 시마다 **배열을 새로 할당하고 초기화**하기 때문에, 성능이 안좋아질 수 있다.

→ **오버로딩(다중 정의)를 사용**해서 미리 호출 확률에 비례해서 **각각 다른 인자를 받는 메서드를 생성**해놓으면 된다.

→ `EnumSet.of()` 정적 팩터리 메서드에서도 해당 기법을 사용해 열거 타입 집합 생성 비용을 최소화하고 있다.

```java
public void foo() {}
public void foo(int a1) {}
public void foo(int a1, int a2) {}
public void foo(int a1, int a2, int a3) {}
public void foo(int a1, int a2, int a3, int... rest) {}
```

```java
@SafeVarargs
public static <E extends Enum<E>> EnumSet<E> of(E first, E... rest) {
    EnumSet<E> result = EnumSet.noneOf(first.getDeclaringClass());
    result.add(first);
    for (E e : rest)
        result.add(e);
    return result;
}
```

## 핵심 정리

인수 개수가 일정하지 않은 메서드를 정의해야 한다면, 가변인수가 반드시 필요하다. 

메서드를 재정의할 때 필수 매개변수는 가변인수 앞에 두고, 가변인수를 사용할 때는 성능 문제까지 고려하자.