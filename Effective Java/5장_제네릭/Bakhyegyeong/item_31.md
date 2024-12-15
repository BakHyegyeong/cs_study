# 아이템 31 : 한정적 와일드카드를 사용해 API 유연성을 높여라

# 매개변수화 타입은 불공변이다


> ⭐
불공변 : 서로 다른 타입 `Type1`, `Type2`가 있을 때 `List<Type1>`이 `List<Type2>`의 하위 타입도 상위 타입도 아닌 것을 의미 <br>
ex) `List<String>`은 문자열만, `List<Object>`는 어떤 객체도 넣을 수 있어 전자는 후자가 하는 일을 제대로 수행하지 못하므로 하위 타입이 될 수 없다.


**매개변수화 타입은 불공변**이며 불공변 방식은 유연하지 않다는 단점이 있다.

→ 가끔씩 유연하게 API를 설계해야하는 상황에서는 이를 위해 **한정적 와일드 카드를 사용해 API의 유연성을 높힌다.**

## 불공변 타입의 단점 예시

- Stack 클래스의 **pushAll**, **popAll** 메서드

```java
public class Stack<E> {
    private List<E> list = new ArrayList<>();
    
    // 결함 존재
    public void pushAll(Iterable<E> src) {
        for(E e : src) {
            push(e);
        }
    }
   
    // 결함 존재
    public void popAll(Collection<E> dst) {
		    while (!isEmpty()) {
		        dst.add(pop());
		    }    
		}
}
```

- pushAll 메서드의 결함
    
    `Iterable`의 원소 타입이 스택의 원소 타입과 일치하면 잘 작동하지만 타입이 다를 경우 매개변수화 타입이 불공변이기 때문에 컴파일 에러가 발생하게 된다.
    
    → 예시로 `Stack<Number>`로 선언 후 `pushAll()`에 `Integer` 타입(하위 타입)의 컬렉션을 전달하면 **매개변수화 타입이 불공변**이기 때문에 오류가 난다. <br>
    → `Integer` 는 `Number` 의 하위 타입이지만, `Iterable<Integer>` 와 `Stack<Number>` 의 관계는 불공변의 특성으로 인해 **하위 타입이 되지 않기 때문이다.**
    
- popAll 메서드의 결함
    
    `Stack<Number>`의 원소를 `Object`용 컬렉션으로 옮기려 한다면 위와 같은 이유로 컴파일 에러가 발생하게 된다.
    

## 불공변 타입의 단점 해결

### 생산자와 소비자에 따른 PECS 공식


> ⭐ **PECS 공식** <br> 1) 매개변수화 타입 T 가 생산자 : `<? extends T>` <br> 2) 매개변수화 타입 T 가 소비자 : `<? super T>`



- 생산자 : `pushAll()`의 인자 **`src`**
    
    → **`src`는 `Stack` 이 사용할 `E` 인스턴스를 생산하기 때문이다.**
    
- 소비자 : `popAll()`의 인자 **`dst`**
    
    → **`dst`는 `Stack` 으로부터 `E` 인스턴스를 소비하기 때문이다.**
    

### 한정적 와일드 카드 타입

```java
public class Stack<E> {
    private List<E> list = new ArrayList<>();

	// 한정적 와일드 카드 타입 적용
    public void pushAll(Iterable<? extends E> src) {
        for(E e : src) {
            push(e);
        }
    }
		
	// 한정적 와일드 카드 타입 적용
    public void popAll(Collection<? super E> dst) {
        while (!isEmpty())
            dst.add(pop());
    }
}
```

- `Iterable<? extends E> src` : **E의 하위 타입의 Iterable**
    
    → 부모 타입인 `E`로만 원소를 꺼낼 수 있게 한다.
    
- `Collection<? super E> dst` : **E의 상위 타입의 Collection**
    
    → 자식 타입인 stack의 원소만을 넣을 수 있게 한다.
    

## 한정적 와일드 카드 타입 주의사항

### 1. 반환 타입에는 사용 불가능

```java
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)
```

### 2. 자바 7 이전 버전은 명시적 타입 인수 사용

```java
Set<Number> numbers = Union.<Number>union(integers, doubles);
```

### 3. **메서드 선언에 타입 매개변수가 한번만 나오면, 와일드 카드로 대체해야 한다**

```java
public static <E> void swap(List<E> list, int i, int j) // 타입 매개변수 
public static void swap2(List<?> list, int i, int j)    // 와일드카드
```

타입 매개변수와 와일드 카드에는 공통되는 부분이 많아서 **둘 중 어느 것을 선택하든 상관없다.**

하지만 와일드 카드로 구현한 swap2 메서드에는 컴파일 오류가 발생한다.

```java
public static void swap(List<?> list, int i, int j){
	list.set(i, list.set(j, list.get(i)));
}
```

**? 타입이 무엇인지 알 수 없기 때문에 리스트의 요소를 변경할 수 없거나  `List<?>` 에는 `null` 외에는 어떤 값도 넣을 수 없기 때문**이다.

### 해결

```java
public static void swap(List<?> list, int i, int j) {
    swapHelper(list, i, j); 
}

//와일드카드 타입을 실제 타입으로 바꿔주는 private 도우미 메서드
private static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i)));    
}
```

위와 같이 도우미 메서드에서 **와일드 카드 타입의 실제 타입**을 알려준다.

→ `swapHelper` 메서드는 아래의 사항을 보장한다.

- **리스트가 `List<E>`** 이다.
- **리스트에서 꺼낸 값이 항상 `E`** 이다.
- **E 타입의 값이라면 이 리스트에 넣어도 안전**하다

## 핵심 정리

조금 복잡하더라도 와일드카드 타입을 적용하면 API가 훨씬 유연해진다. 

생산자는 extends를 소비자는 super를 사용해야 한다는 PECS 공식을 꼭 기억하자. 또한, Comparable과 Comparator는 모두 소비자라는 사실도 잊지 말자.