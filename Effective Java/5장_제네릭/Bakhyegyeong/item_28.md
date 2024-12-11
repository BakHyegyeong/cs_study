# 아이템 28 : 배열보다는 리스트를 사용하라

# 배열과 제네릭의 차이점

## 1. 공변(covariant)의 여부


> 💡 공변 : 함께 변한다는 것 <br>
→ 자신이 상속받은 부모 객체로 타입을 변화시킬 수 있는 것


- 배열 : 공변
→ `Sub` 가 `Super` 의 하위 타입이라면 `Sub[]` 또한 `Super[]`의 하위 타입이다.

```java
Object[] objectArray = new Long[1];
objectArray[0] = "저장 안되는 문자열"; //ArrayStoreException을 던진다.
```

`Long` 이 `Object` 의 하위 타입이 되기 때문에 컴파일은 되지만 잘못된 타입을 넣는 다면 런타임에 오류가 나버린다.

→ 하지만 공변 덕분에 배열은 다형성을 활용할 수 있다.

- 제네릭 : 불공변
→ 서로 다른 타입 `Type1` 과 `Type2` 가 있을 때 `List<Type1>`은 `List<Type2>`의 하위 타입과 상위 타입 모두가 아니다.

```java
List<Object> o1 = new ArrayList<Long>(); // 호환되지 않는 타입
o1.add("저장 안되는 문자열");
```

컴파일 시에 오류가 발생한다.

→ 타입 안전성을 보장할 수 있다.

에러를 컴파일 시에 바로 알아차릴 수 있는 제네릭 타입이 더 선호된다.

## 실체화(reify) 여부


> 💡 **실체화** : 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다.

- 배열 : **실체화** 된다. (런타임에도 자신의 원소 타입을 인지하고 확인한다.)
- 제네릭 : **타입 정보가 런타임에는 소거**되어, 원소 타입을 컴파일 타임에만 검사하며 런타임에는 알 수 조차 없다.
→ `E`, `List<E>`, `List<String>` 과 같은 타입은 **실체 불가능화 타입**(non-reifiable type)이라고 한다.

위의 같은 특징으로 인해 배열과 제네릭은 잘 어우러지지 못한다.

**배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없다.** 

즉, 코드를 `new List<E>[]`, `new List<String>[]`, `new E[]`식으로 작성하면 컴파일할 때 제네릭 배열 생성 오류가 난다.


> 💡 소거 : 제네릭이 지원되기 전의 레거시 코드와 제네릭 타입을 함께 사용할 수 있는 메커니즘으로, 자바 5가 제네릭으로 순조롭게 전환될 수 있도록 해준다. <br>
→ 소거 메커니즘으로 인해 `List<?>` 와 같은 비한정적 와일드카드 타입만 실체화 가능하다.


## 제네릭 배열 생성을 허용하지 않는 이유

제네릭 배열은 **타입 안전하지 않다**는 이유로 만들지 못하게 막아져 있다. 

**컴파일러가 자동 생성한 형변환 코드에서 런타임에 `ClassCastException` 이 발생할 수 있기 때문**이다.

→ 런타임에 `ClassCaseException`이 발생하는 일을 막아주겠다는 제네릭의 취지에 어긋난다.

```java
List<String>[] stringLists = new List<String>[1];
List<Integer> intList = List.of(42);
```

```java
Object[] objects = stringLists;  // 배열은 공변이니 대입 가능
objects[0] = intList;            // 제니릭은 실체화가 안되니, 대입 가능
String s = stringList[0].get(0); // 결과 에러 발생
```

위의 코드를 보면 `List<String>` 인스턴스만 담겠다고 선언한 `stringLists`에 `List<Integer>` 인스턴스가 저장되어 있다. 

`Object[]` 로 변환해줌으로써 **불공변의 컴파일 에러를 피해갔고, 런타임에는 타입 정보가 사라지니 저장하는 것이 가능**해졌기 때문이다.

따라서 마지막 줄에서 원소를 꺼내려 할 때 `String` 변수에 `Integer` 타입 값을 저장하려 하여 런타임 상 오류인 `ClassCastException`이 발생한다.

## 배열보다는 리스트를 사용하자

배열을 형변환할 때 제네릭 배열 생성 오류나, 비검사 형변환 경고가 뜨는 경우 대부분 배열인 `E[]`대신 컬렉션인 `List<E>` 를 사용하면 해결된다.

성능은 나빠질 수 있지만, 타입 안전성과 상호 운용성이 좋아진다.

### 배열

```java
public class Chooser {
    private final Object[] choiceArray;
    
    public Chooser(Collection choices) {
        choiceArray = choices.toArray();
    }
    
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray(rnd.nextInt[choiceArray.length)];
}
```

이 클래스를 사용하려면 **choose 메서드를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환**해야 한다. 

만약 다른 타입의 원소가 들어있었다면 런타임에 형변환 오류가 발생한다.

### 제네릭 배열

```java
public class Chooser<T> {
    private final T[] choiceArray;
    
    public Chooser(Collection<T> choices) {
        choiceArray = choice.toArray();
    }
}
```

**컴파일러는 `T` 가 무슨 타입인지 알 수 없으니 이 형변환이 런타임에도 안전할지 보장할 수 없다**는 경고를 띄운다. 

제네릭에서는 원소의 타입 정보가 소거되어 런타입에는 무슨 타입인지 알 수 없다. 

### 제네릭 리스트

```java
public class Chooser<T> {
    private final List<T> choiceList;
    
    public Chooser(Collection<T> choices) {
        choiceList = new ArrayList<>(choices);
    }
    
    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

## 핵심 정리

- 배열은 런타임에는 타입 안전하지만 컴파일시에는 그렇지 않다.
- 제네릭은 배열과 반대이다.
- 되도록이면 제네릭을 사용하고 배열과 리스트를 섞어 사용하다 경고를 만날경우 배열을 리스트로 대체하자.