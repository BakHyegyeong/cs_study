# 아이템 26 : 로 타입은 사용하지 말라

# 제네릭 타입

타입 매개변수(`<T>`)가 사용된 클래스와 인터페이스를 통틀어 부르는 것

제네릭 타입을 하나 정의하면, 그에 딸린 로 타입도 함께 정의된다.

# 로 타입

제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때를 의미하며 제네릭 타입 정보가 전부 지워진 것처럼 동작한다.

ex) `List<E>`의 로 타입은 `List`이다.

즉 타입 매개변수가 사용되지 않은 클래스나 인터페이스를 의미한다.

## 로 타입의 단점

```java
// Collection 뒤에 <>가 사용되지 않았다.
private final Collection stamps = RawType.getStamps();
stamps.add(new Coin(...));

for(Iterator i = stamps.iterator(); i.hasNext(); ){
	Stamp stamp = (Stamp) i.next();  //ClassCastException
    stamp.cancle();
 }
```

Stamp타입만 저장하는 코드이지만 Coin타입의 객체를 넣어도 **컴파일 오류는 발생하지 않는다.**

실행을 한 후에 **런타임 에러`(ClassCastException)`가 발생**하게 된다.

## 로 타입보다 제네릭을 사용해야 하는 이유

```java
// Collection<Stamp>
private final Collection<Stamp> stamps = RawType.getStampsWithGenerics();
// 매개변수화된 컬렉션 타입으로 선언한 경우 잘못된 객체를 넣으면 컴파일 오류가 발생한다.
stamps.add(new Coin(...));

for(Iterator i = stamps.iterator(); i.hasNext(); ){
	Stamp stamp = (Stamp) i.next();  //ClassCastException
    stamp.cancle();
 }
```

위와 같이 선언할 경우 **타입 안전성을 확보**할 수 있고 로 타입을 사용할 경우 제네릭이 안겨주는 **안정성과 표현력을 모두 잃게 된다.**

로 타입은 자바 1.4 이하 버전에 작성된 코드와의 호환성을 지키기 위해 사용된다.

## `List`와 `List<Object>`

`List`는 로 타입으로 사용해서는 안되나, `List<Object>`처럼 임의의 객체를 허용하는 매개변수화 타입은 사용할 수 있다.

### 제네릭의 하위 타입 규칙

매개변수로 List를 받는 메서드에는 `List<String>`을 넘길 수 있지만, `List<Object>`를 받는 메서드에는 넘길 수 없다.

→ 제네릭의 하위 타입 규칙인 **불공변** 특성으로 인해, `List<String>` 는 `List`의 하위타입으로,  `List<Object>` 는 `List`의 하위 타입이 아닌 아예 **다른 타입으로 간주**하기 때문이다.

→ 따라서 **`List<Object>` 같은 매개변수화 타입을 사용할 때와 달리 `List` 같은 타입을 사용하면 타입 안정성을 잃게된다.**

- 로 타입

```java
public class Raw {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        // String 컬렉션에 Integer를 넣고 있다.
        unsafeAdd(strings, Integer.valueOf(42));
        String s = strings.get(0);  // 컴파일러가 자동으로 형변환 코드를 넣어준다.
    }

    private static void unsafeAdd(List list, Object o) { // 컴파일 에러 X
        list.add(o);
    }
}
```

String 컬렉션에 Integer를 넣는 것은 가능하지만 꺼낼 때 런타임 에러가 발생한다.

- `List<Object>`

```java
public class Raw {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        unsafeAdd(strings, Integer.valueOf(42));
        String s = strings.get(0);
    }

    private static void unsafeAdd(List<Object> list, Object o) {  // 컴파일 에러
        list.add(o);
    }
}
```

## 비한정적 와일드 카드

와일드 카드(`?`)란, 제네릭 타입을 쓰고 싶지만 **실제 타입 매개변수가 무엇인지 신경쓰고 싶지 않을 경우** 사용할 수 있는 것을 의미한다.

ex) 제네릭 타입인 `Set<E>`의 비한정적 와일드카드 타입은 `Set<?>`이다.

로 타입에는 아무 원소나 넣을 수 있어 타입 불변식을 훼손하기가 쉽다. 반면 **비한정적 와일드카드는 `null`을 제외한 어떤 원소도 넣을 수 없다.** 즉, 다른 원소를 넣으려 하면 컴파일할 때 에러를 발생하게 된다.

→ 이 문제를 해결하기 위해 한정적 와일드 카드타입이 나왔다.

## 로 타입을 사용해야 하는 예외

### class 리터럴에는 로 타입을 써야 한다. 
   
자바 명세는 배열과 기본 타입 외의 `class` 리터럴에 매개변수화 타입을 사용하지 못하게 하고 있다. 예를 들어, `List.class`는 허용하지만 `List<String>.class`는 불가능하다.


> 💡 class 리터널 : `String.class`, `Integer.class` 등을 말하며 하나의 객체로 생각할 수 있다.


### instanceof 연산자를 사용해야 할 경우는 로 타입이나 비한정적 와일드카드 타입을 사용한다. 
   
**런타임에는 제네릭 타입 정보가 지워지므로**, `instanceof` 연산자는 비한정적 와일드카드 타입 이외의 매개변수화 타입에는 적용할 수 없다. <br>
따라서 **로 타입이나 비한정적 와일드카드 타입이든 같은 결과를 내기 때문에**, 차라리 더 깔끔한 로 타입을 사용하기도 한다.

```java
if (o instance of Set){   // 로 타입
	Set<?> s = (Set<?>) o;    // 와일드카드 타입
	...
{
```

`o`의 타입이 `Set` 임을 확인한 다음 비한정적 와일드 카드 타입으로 형변환 하고 있다. 검사 형변환이므로, 컴파일러 경고가 뜨지 않는다.

## 핵심 정리

로 타입을 사용하면 런타임에 예외가 일어날 수 있으니 사용하면 안 된다. `Set<Object>`는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이고, `Set<?>`는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입이다. 그리고 이 들의 로 타입인 `Set`는 다른 것들과 달리 안전하지 않다.
