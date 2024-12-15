# 아이템 30 : 이왕이면 제네릭 메서드로 만들라

# 제네릭 메서드

## 메서드에서의 로 타입

```java
public static Set union(Set s1, Set s2) {
		// 로 타입 사용
    Set result = new HashSet(s1); // 경고
    result.addAll(s2);  // 경고
    return result;
}
```

매개변수의 타입이나 반환 타입으로 로 타입을 사용하게 되면 컴파일은 가능하지만 `new HashSet`을 하는 과정과 result에 s2를 `addAll` 하는 과정에서 **로 타입에 대한 경고가 발생**한다.

## 제네릭 메서드 사용 방법

1. 메서드 선언에서의 세 집합(입력 2개와 반환 1개)의 원소 타입을 **타입 매개변수로 명시**한다.
2. 메서드 안에서도 **이 타입 매개변수만 사용하게 수정**한다.

```java
public static <E> Set<E> union (Set<E> s1, Set<E> s2){
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

**타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 위치한다.**

위의 코드에서 타입 매개변수 목록은 `<E>` 이고 반환 타입은 `Set<E>` 가 된다.


> ⭐<br> - 타입 매개변수 목록 : 제네릭 메서드에서 **제네릭 타입을 정의하는 부분** <br> - 제한자 : static, synchronized와 같은 제약 조건


## 제네릭 싱글턴 팩터리 패턴

제네릭은 런타임 시점에 Object 타입으로 타입이 소거되므로 **하나의 객체를 어떤 타입으로든 매개변수화 할 수 있다.** 

→ 이를 위해 **요청한 타입에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리**를 만들어야 한다.

→ 이러한 패턴을 **제네릭 싱글턴 팩터리**라고 한다.

### Collections.reverseOrder , Collections.emptySet

```java
public class GenericFactoryMethod {
    private static final Set IMMUTABLE_EMPTY_SET = Set.copyOf(new HashSet());

    @SuppressWarnings("unchecked")
    public static <T> Set<T> immutableEmptySet() {
        return (Set<T>) IMMUTABLE_EMPTY_SET;
    }
}
```

`immutableEmptySet`은 **원소 타입으로 어떤 타입을 요청해도 불변의 빈 Set을 돌려주는 메서드**이다.

→ 새로 element가 추가될 가능성도 없고 Set 내부에 element가 없으므로 `T`에 어떤 타입 요청이 오더라도 **이 비검사 형변환의 타입 안전을 보장**할 수 있다.

### 항등함수

```java
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarinings("unchecked")
public static <T> UnaryOperator<T> identityFunction(){
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

**입력 값을 수정 없이 그대로 반환하는 특별한 함수**이므로 `T`가 어떤 타입이든 `UnaryOperator<T>`를 사용해도 타입 안전하다.

## 재귀적 타입 한정

**자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정**하는 것

```java
public static <E extends Comparable<E>> E max(Collection<E> c){
	if(c.isEmpty())
		throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
	
    E result = null;
	for (E e :  c)
		if(result == null || e.compareTo(result) > 0)
			result = Objects.requireNonNull(e);
    return result;
}

```

`max` 메서드는 Collection의 요소들 중 가장 큰 요소를 반환하는 메서드이다.

→ 이를 위해 **매개변수로 받아오는 컬렉션의 요소들은 비교 및 정렬이 가능해야 한다.**

→ 이를 위해 매개변수 타입을 넣어주는 부분에서 **`<E extends Comparable<E>>`를 넣어줌으로써 Comparable의 하위 구현체인 타입만 올 수 있다는 것을 명시**해 주었다.

## 핵심 정리

제네릭 타입과 마찬가지로, **클라이언트에서 입력 매개변수와 반환값을 명시적으로 형변환해야 하는 메서드보다 제네릭 메서드가 더 안전**하며 사용하기도 쉽다. 기존 클라이언트는 그대로 둔 채 새로운 사용자의 삶을 훨씬 편하게 만들어줄 것이다.