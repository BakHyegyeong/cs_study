# 아이템 33 : 타입 안전 이종 컨테이너를 고려하라

# **타입 안전 이종 컨테이너 패턴**

**컨테이너 자체가 아닌 "키"를 매개변수화한 다음, 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공해 제네릭 타입 시스템의 값 타입이 키와 같음을 보장하는 패턴**

제네릭은 하나의 컨테이너에서 매개변수화할 수 있는 타입의 수가 제한된다.

→ 예를 들어, `Set<E>`에는 원소의 타입을 뜻하는 단 하나의 타입 매개변수만 있으면 되며, `Map<K,V>` 에는 키와 값을 뜻하는 2개만 필요하다.

→ 타입 안전 이종 컨테이너 패턴을 사용하면 **더 유연하게 여러 타입을 받을 수 있다.**

```java
public <T> void putFavorite(Class<T> type, T instance);   // 키가 매개변수화 됨
public <T> T getFavorite(Class<T> type);
```

## 구현

사실 `class` 의 타입을 `Class<T>` 제네릭으로 표현할 수 있기 때문에, **각 타입의 `Class` 객체를 매개변수화한** **키 역할로 사용**하는 것이 가능해진다. 

ex) `String.class` 타입은 `Class<String>`

→ 제네릭에서의 컴파일타임 타입 안전성이 아닌 **런타임 타입 안전성**을 얻을 수 있다

```java
public class Favorites{
    private Map<Class<?>, Object> favorites = new HashMap<>();

    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), instance);
    }

    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
}
```

1. `Map<Class<?>, Object>`
    
    키를 **비한정적인 와일드카드 타입**으로 선언하였기 때문에, 이를 통해서 **다양한 매개변수화 타입의 키**를 허용할 수 있게 되었다. 
    
    ```java
    Favorites favorites = new Favorites();
    favorites.putFavorite(String.class, "Hello");
    favorites.putFavorite(Integer.class, 42);
    ```
    
    위의 경우 favorites의 `Map<Class<?>, Object>`는 String.class와 Integer.class를 키로 사용할 수 있다.
    하지만 `Map<Class<T>, Object>`였다면, 한 번 T가 정해지면 다른 타입의 키를 추가할 수 없으므로, 유연성이 떨어진다.
    
2. `Class.cast`
    
    `Map<Class<?>, Object>`에서 `value` 가 `Object` 타입이므로 맵에 넣을때 값이 키 타입의 인스턴스라는 것이 보장되지 않는다. 
    
    따라서 맵에서 가져올때는 **`cast` 메서드를 사용해** 이 객체 참조를 `class` 객체가 가리키는 `T` 타입으로 동적 변환하고 있다.
    
    ```java
    public <T> T getFavorite(Class<T> type){
        return type.cast(favorites.get(type));
        // Object 타입의 객체(favorites.get(type)를 꺼내 T로 바꿔 반환해야 한다.
        // cast메서드로 이 객체 참조를 Class 객체가 가리키는 타입으로 동적 형변환 한다.
    } 
    
    String favoriteString = favorites.getFavorite(String.class);
    Integer favoriteInteger = favorites.getFavorite(Integer.class);
    Class<?> favoriteClass = favorites.getFavorite(Class.class);
    ```
    

이렇게 Favorites 인스턴스는 **타입 안전**하며 일반적인 맵과 달리 **여러 가지 타입의 원소를 담을 수 있기 때문에, 타입 안전 이종 컨테이너**라고 할 수 있다.


> ⭐ **cast 메서드** <br>
형변환 연산자의 동적 버전으로, 주어진 인수가 Class 객체가 알려주는 타입의 인스턴스인지 검사한 다음, 맞다면 반환하고 아니면 `ClassCastException` 을 던진다. 

이를 활용하면, `T` 로 비검사 형변환을 하지 않아도 된다.

```java
public class Class<T>{
	T cast(Object obj);
}
```

</aside>

## 타입 안전 이종 컨테이너의 한계

### 1. **Class 객체를 제네릭이 아닌 로 타입으로 넘기면, 타입 안전성이 쉽게 깨진다.**

아래 코드에서는 `Class` 로 타입으로 다시 캐스팅하여 전달했으니, 컴파일 타임에서는 문제 없이 `Map` 에 저장이 된다. 

이후 꺼내올때 `String` 객체를 `Integer` 로 캐스팅 하려 하니 런타임에서 예외가 발생한다.

```java
f.putFavorite((Class)Integer.class, "문자열");
int result = f.getFavorite(Integer.class);  // ClassCastException
```

### 2. **실체화 불가 타입에서 사용할 수 없다.**

`String`이나 `String[]`은 사용할 수 있지만 **`List<String>`은 사용할 수 없다.**

→ `List<String>`용 `Class` 객체를 얻을 수 없기 때문에 `List<String>`을 사용하려는 코드는 컴파일 되지 않는다.

ex) **`List<String>.class`라고 쓰면 문법 오류가 발생한다.** 

→ `List<String>`과 `List<Integer>`는 `List.class`라는 같은 `Class` 객체를 공유하므로 **같은 타입의 객체 참조를 반환한다면 객체 내부에서 이들을 구분할 방법이 없어진다.**

반드시 제네릭을 저장하고 싶다면, 대안으로 스프링에서는 슈퍼 타입 토큰 즉 `ParameterizeTypeReference` 라는 클래스로 제공하고 있다.

## 한정적 타입 토큰

Favorites가 **어떤 Class 객체든 받아들이므로 비한정적 타입 토큰**이라 할 수 있다. 

**→ 이 메서드들이 허용하는 타입을 제한**하고 싶다면, 한정적 타입 토큰을 활용할 수 있다.


> ⭐ **한정적 타입 토큰이란?** <br>
한정적 타입 매개변수(E extends Delayed)나 한정적 와일드카드(? extends Delayed)을 사용하여 **표현 가능한 타입을 제한하는 토큰**



```java
// 대상 요소에 달려있는 애너테이션을 런타임에 읽어 오는 기능
public <T extends Annotation> T getAnnotation(Class<T> annotationType);
```

`annotationType`인수는 **Annotation** **타입을 뜻하는 한정적 타입 토큰**이다.

이 메서드는 토큰으로 명시한 타입의 애너테이션이 대상 요소에 달려 있다면 그 애너테이션을 반환하고, 없다면 Null을 반환한다.

## asSubClass

`asSubclass`메서드를 이용해 **호출된 인스턴스 자신의 `Class` 객체를 인수가 명시한 클래스로 형변환**한다.

→ 한정적 타입토큰을 안전하게 형변환하기 위해 사용된다.

```java
static Annotation getAnnotation(AnnotatedElement element,
                                    String annotationTypeName) {
     Class<?> annotationType = null; // 비한정적 타입 토큰
     try {
         annotationType = Class.forName(annotationTypeName);
     } catch (Exception ex) {
         throw new IllegalArgumentException(ex);
     }
     
     return element.getAnnotation(
            annotationType.asSubclass(Annotation.class));  //형변환
}
```

## 핵심 정리

컬렉션 API로 대표되는 일반적 제네릭 형태에서는 한 컨테이너가 다룰 수 있는 타입 매개변수의 수가 고정되어 있다. 

하지만 컨테이너 자체가 아닌 키를 타입 매개변수로 바꾸면 이런 제약이 없는 타입 완전 이종 컨테이너를 만들 수 있다. 

타입 안전 이종 컨테이너는 Class를 키로 쓰며, 이런 식으로 쓰이는 Class 객체를 타입 토큰이라 한다.