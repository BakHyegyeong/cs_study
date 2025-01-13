# 아이템 54 : null이 아닌, 빈 컬렉션이나 배열을 반환하라

# Null 반환 메서드

컬렉션에 요소가 없어 null을 반환하는 경우에는 **방어 코드를 넣어주어야 한다.**

```java
private final List<Cheese> cheesesInStock = ...;

public List<Cheese> getCheeses() {
	return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
	
// 방어 코드
List<Cheese> cheeses = shop.getCheeses();
if (cheeses != null && cheeese.contains(Cheese.STILTON))
	System.out.println("좋았어, 바로 그거야.");	
```

`cheeses != null`과 같이 **null을 확인해야 하는 의무**를 갖게 된다.

## Null 을 반환하지 않는 메서드 사용

빈 컬렉션을 반환하는 것보다 **null을 반환하는게 비용이 낮다는 주장도 있지만 이는 틀린 주장이다.**

1. 새로운 객체를 하나 만들지만 사실상 성능상의 커다란 손해가 없다.
2. 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환이 가능하다. 
    
    ```java
    // 빈 컬렉션을 불변으로 선언하고 재활용
    private final List<Car> cars;
    
    public List<Car> getCars() {
        return new ArrayList<>(cars);
    }
    
    // 불변 컬렉션 사용
    Collections.emptyList()
    Collections.emptySet()
    Collections.emptyMap()
    ```
    

### 빈 컬렉션 반환

`null` 대신 비어있는 컬렉션을 할당한다면, 방어 코드를 작성할 필요가 없어진다.

```java
public List<Cheese> getCheeses () {
	return new ArrayList<>(cheeseInStock);
}
```

사용 패턴에 따라 빈 컬렉션 할당이 **성능을 눈에 띄게 떨어뜨릴 수도 있다.**

→ 매번 똑같은 불변 컬렉션을 반환해 해결할 수 있다.

```java
Collections.emptyList();
Collections.emptySet();
Collections.emptyMap();

// 최적화된 사용 : 빈 컬렉션을 매번 새로 할당하지 않고 
// 필요할 때만 사용한다.
public List<Cheese> getCheese() {
	return cheesesInStock.isEmpty() ? 
    	Collections.emptyList() : new ArrayList<>(cheesesInStock);
}
```

### 빈 배열 반환

배열을 반환할때도 null을 반환하지말고 **길이가 0인 배열을 반환**한다.

```java
public Cheese[] getCheeses() {
	return cheesesInStock.toArray(new Cheese[0]);
}

// 최적화된 사용 : 빈 배열을 매번 새로 할당하지 않고
// 길이가 0인 배열을 미리 선언해두고 매번 선언한 배열을 반환
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {
	return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```

`toArray()`는 배열의 크기에 따라 동작이 달라지는데, 배열이 충분히 크면 원소를 담아 반환하고, 그렇지 않으면 배열을 새로 만들어 그 안에 원소를 담아 반환한다.

→ 원소가 하나라도 있다면 `Cheese[]` 타입 배열을 새로 생성해 반환하고, 원소가 0개면 `EMPTY_CHEESE_ARRAY`를 반환한다.

## 핵심 정리

null이 아닌, 빈 배열이나 컬렉션을 반환하라. 

null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어나며, 성능이 좋은 것도 아니기 때문이다.