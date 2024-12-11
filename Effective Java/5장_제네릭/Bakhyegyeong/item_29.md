# 아이템 29 : 이왕이면 제네릭타입으로 만들라

# 일반 클래스를 제네릭 타입으로 변경

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
```

`Object` 기반 스택은 스택에서 객체를 꺼낼 때 형변환 과정을 거쳐야 하는데 이때 `ClassCastException` 오류가 발생할 수 있다.

따라서 이 부분을 제네릭 타입으로 바꾸는 과정을 거친다.

## 제네릭 변경

1. 클래스 선언에 타입 매개변수를 추가한다 ( 타입 이름 : `E` )
2. 코드에 쓰인 `Object` 를 적절한 타입 매개변수로 바꾼다

```java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new E[DEFAULT_INITIAL_CAPACITY]; // 예외 발생
    }
```

하지만 **제네릭은 실체화 할 수 없으므로** 이 방법은 사용할 수 없다.

## 제네릭 변경 우회 방법

### 배열을 Object로 생성하고 제네릭으로 형 변환

```java
@SuppressWarnings("unchecked")
public GenericStack() {
    elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY]; // 경고 메시지 발생
}

public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
}
```

컴파일은 되지만 `Unchecked cast: 'java.lang.Object[]' to 'E[]` 와 같은 비검사 형변환 경고가 발생한다.

- `elements` 배열은 `private` 필드에 저장된 후 클라이언트로 반환되거나 **다른 메서드에 전달되지 않는다.**
- `push` 메서드를 통해 **배열에 저장되는 원소의 타입은 항상 `E`**이다.

→ 위의 두 특성으로 **이 비검사 형변환이 안전하다는 것이 증명**되었고 
비검사 형변환 경고를 없애기 위해 범위를 최대한 줄여 `@SuppressWarnings("unchecked")`를 사용하여 경고를 숨긴다.

### elements의 타입을 Object[]로 바꾸고 pop에서 형변환

```java
public class Stack<E> {
    private Object[] elements;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    ...
    
    public E pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        
				// 
        @SuppressWarnings("unchecked")
        E result = (E) elements[--size];
        elements[size] = null; // 다 쓴 참조 해제

        return result;
    }
}
```

Object[]로 바꾼 후 컴파일러는 **pop 메서드 안의 E 타입 형변환이 안전한지 알 수 없다.**

하지만 **push 메서드에서 E 타입만 허용하므로 이 형변환은 안전**하다.

따라서 `@SuppressWarnings("unchecked")` 를 사용하여 경고를 숨길 수 있다.

### 차이점

**방법 1**

- 배열을 E[]로 선언하여 E 타입만 받음을 명시해준다.
- 가독성이 좋고 코드가 짧다.
- 런타임에는 E[]가 아닌 Object[]로 동작한다.
    
    → 힙 오염의 가능성이 존재한다.
    

**방법 2**

- Object[] 배열이므로 힙 오염의 가능성이 없다.
- push 메서드로 E만 들어오므로 Object[]에 저장되는 데이터가 모두 E 타입임이 보장된다. 
→ pop 메서드 안의 타입 보장이 된다.
- pop 메서드 호출시마다 타입 캐스팅을 해주어야 한다.

## 힙 오염

**매개변수화 타입의 변수가 타입이 다른 객체를 참조**하게 되어, 힙 공간에 문제가 생기는 현상

→ **컴파일은 가능하지만 런타임에 `ClassCastException` 오류를** 발생시킨다.

```java
@SuppressWarnings("unchecked")
void contextLoads(){
		ArrayList<String> list1 = new ArrayList<>();
		ArrayList<Integer> list2 = (ArrayList<Integer>) (Object) list1;
		// list1을 ArrayList<Integer>로 변환
		
		list2.add(100);
		list2.add(200);
		
		String str = list1.get(0): // Runtime Exception
}
```

해당 코드는 `list1`에 상속 관계도 아닌 **서로 다른 두 타입의 객체가 추가**되었다. 

말이 안되지만, 컴파일러는 잘못된 상황을 다음과 같은 두 가지 이유로 알아차리지 못하게 된다.

1. **타입 캐스팅 체크는 컴파일러가 하지 않는다.** 오직 대입되는 참조변수에 저장할 수 있느냐만 검사한다.
2. **제네릭의 소거 특성**으로 인해 컴파일이 끝난 클래스 파일의 코드에는 타입 파라미터 대신 `Object` 가 남아있게 된다. 
→ **`ArrayList<Object>` 에는 `String` 이나 `Integer` 모두 넣는 것이 가능해진다.**

따라서 실행을 시키고 꺼내는( `get` ) 코드가 실행되고 나서야 **런타임 캐스팅 예외**가 발생한다. 

컬렉션으로 요소를 꺼내올 때 **컴파일러가 해당 객체의 제네릭 타입 파라미터로 캐스팅하는 문장을 자동으로 삽입**해주기 때문이다.

```java
String str = (Integer)arrayList.get(0);  // 자동 변환
```

### raw 타입과 매개변수화 타입을 같이 사용하는 경우

```java
@SuppressWarnings("unchecked")
void contextLoads(){
		List in = new ArrayList<Number>();
		
		List<String> list;
		list = in; // unchecked warning + heap pollution
		list.add("100"); // 소거 특성으로 인해 저장
		
		Number num = (Number) in.get(0); // runtime casting exception 발생
```

### 힙 오염 해결 방안

근본적인 원인을 잡기 위해서는, 꺼낼때가 아닌 넣을 때를 검사해야 한다. 

**해당 리스트의 제네릭 타입이 아닌 다른 타입의 요소를 저장할 때 바로 예외를 발생**시켜주는 감시자인 `Collections.checkedList` 를 사용하면 된다.

```java
E typeCheck(Object o){
		if( o != null & !type.isInstance(o))
				throw new ClassCastException(badElementMsg(o));
		return (E) o;
}
```

## 타입 매개변수 제약

- `<? extends 상위타입>` : 와일드카드의 범위를 특정 객체의 하위 클래스만 올 수 있다.
    
    → 상한 경계
    
- `<? super 하위타입>` : 와일드카드의 범위를 특정 객체의 상위 클래스만 올 수 있다.
    
    → 하한 경계
    

## 핵심 정리

제네릭 타입을 사용하면, **클라이언트에서 직접 형변환 하지 않아도 되어 쓰기 편하고 더 안전**하다. <br>
기존 클라이언트에는 아무 영향을 주지 않으면서, 새로운 사용자를 훨씬 편하게 해주는 길이다.