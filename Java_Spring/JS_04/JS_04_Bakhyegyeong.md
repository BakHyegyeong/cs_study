# 제네릭

클래스 내부에서 사용할 **데이터 타입을 외부에서 저장**하는 기법

→ 객체(Object)에 타입을 지정해주는 **타입을 변수화** 한 기능

- 파라미터 타입이나 리턴 타입에 대한 정의를 외부로 미룬다.
- 타입을 미리 정의하지 않기에 타입에 대해 **유연성과 안정성**을 확보한다.
- **런타임 환경에 아무런 영향이 없는 컴파일 시점의 전처리 기술이다.**
    
    → 실행 시 타입 에러가 나는것보다는 컴파일 시에 미리 타입을 강하게 체크해서 **에러를 사전에 방지**할 수 있다.
    

```java
List<Interger> list1 = new ArrayList<>();
List list2 = new ArrayList<>();
Map<String, String> map = new ArrayList<>();
```

위의 코드에서 `<>` 처리된 부분이 제네릭이다.

`<>` 안에 타입명을 지정해 사용한다.

## 제네릭 타입 매개변수/파라미터

`<>`안에 데이터 타입이 아닌 **식별자 기호를 지정해 사용**하는 것

### 클래스, 인터페이스, 메서드 사용

```java
class FruitBox<T> {
    List<T> fruits = new ArrayList<>();

    public void add(T fruit) {
        fruits.add(fruit);
    }
}
```

**클래스 명과 클래스 필드, 메서드의 매개변수 타입**에 각각 `<T>`가 정의되어 있다.

→ 제네릭 클래스가 아닌 일반 클래스 내부에도 제네릭 메서드를 정의할 수 있다.

```java
// 제네릭 타입 매개변수에 정수 타입을 할당
FruitBox<Integer> intBox = new FruitBox<>();
// 다음과 같이 new 생성자 부분의 제네릭의 타입 매개변수는 생략할 수 있다.

// 제네릭 타입 매개변수에 실수 타입을 할당
FruitBox<Double> intBox = new FruitBox<>(); 

// 제네릭 타입 매개변수에 문자열 타입을 할당
FruitBox<String> intBox = new FruitBox<>(); 

// 클래스도 넣어줄 수 있다. (Apple 클래스가 있다고 가정)
FruitBox<Apple> intBox = new FruitBox<Apple>();
```

후에 이 클래스를 인스턴스화할 때 **`<>`안에 타입을 할당하면 클래스 내부의 `<T>`가 지정된 타입으로 모두 변환**된다.

→ 이 과정을 **구체화**라고 한다.

> ⭐ 구체화 : 실행부에서 타입을 받아와 내부에서 T 타입으로 지정한 멤버들에게 전파해 타입이 구체적으로 설정되는 것


### 복수 타입 파라미터

제네릭 타입의 구분은 `<>` 안에서 쉽표(,)로 하며`<T, U>` , `<K, V>`와 같은 형식을 통해 복수 타입 파라미터를 지정할 수 있다.

```java
class ExMultiTypeGeneric<K, V> implements Map.Entry<K,V>{

    private K key;
    private V value;

    @Override
    public K getKey() {
        return this.key;
    }

    @Override
    public V getValue() {
        return this.value;
    }

    @Override
    public V setValue(V value) {
        this.value = value;
        return value;
    }
}
```

### 와일드 카드

- 제네릭타입<?> : 타입 파라미터를 대치하는 것으로 모든 클래스나 인터페이스타입이 올 수 있다.
- 제네릭타입<? extends 상위타입> : 와일드카드의 범위를 특정 객체의 하위 클래스만 올 수 있다.
    
    → 상한 경계
    
- 제네릭타입<? super 하위타입> : 와일드카드의 범위를 특정 객체의 상위 클래스만 올 수 있다.
    
    → 하한 경계
    

```java
public class Calcu { 
	// <?>
	public void printList(List<?> list) { 
    	for(Object obj : list) { 
        	system.out.println(obj + " ");
        }
    }
    
    // <? extends Number> : Number를 상속받는 Integer, Double..
    public int sum(List<? extends Number> list){
    	int sum =0;
        for(Number i : list) { 
        	sum += i.doubleValue();
        }
        return sum;
    }
    
    // <? super Integer> : Integer나 Integer의 상위 클래스인 Number
    public List<? super Integer> addList(List<? super Integer> list) {
    for(int i= 1; i<5; i++) {
    	list.add(i);
    }
    
    return list;
   	}
}
```

### 제네릭 주의할 점

1. 제네릭은 **Wrapper 클래스**만 할당받을 수 있다.
    
    → int형 이나 double형 같은 **자바 원시 타입(Primitive Type)을 제네릭 타입 파라미터로 넘길 수 없다.**
    
2. 제네릭 타입의 객체는 생성할 수 없다.
    
    → `T t = new T();` 는 불가능하다.
    
3. static 멤버에 제네릭 타입은 불가능하다.
    
    →  static 멤버는 클래스가 동일하게 공유하는 변수로서 **제네릭 객체가 생성되기도 전에 이미 자료 타입이 정해져 있어야 하기 때문**
    

## 제네릭 타입 인자

| 기호 | 설명 | 예시 |
| --- | --- | --- |
| `<T>` | 타입(Type) |  |
| `<E>` | 요소(Element) | 예: List |
| `<K>` | 키(Key) | 예: Map<K, V> |
| `<V>` | 리턴 값 또는 매핑된 값(Variable) |  |
| `<N>` | 숫자(Number) |  |
| `<S, U, V>` | 2번째, 3번째, 4번째에 선언된 타입 |  |
| `<R>` | 결과(Result) |  |
