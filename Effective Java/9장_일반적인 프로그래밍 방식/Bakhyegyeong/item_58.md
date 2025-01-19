# 아이템 58 : 전통적인 for 문보다는 for-each 문을 사용하라

# 전통적인 for 문의 문제점

```java
// 컬렉션 순회
for (Iterator<Element> i = c.iterator(); i.hasNext(); ){
	Element e = i.next();
    ... 
}

// 배열 순회
for (int i = 0; i < a.length; i++) {
    ...
}
```

while문보다는 낫지만 가장 좋은 방법은 아니다.

- 반복자와 인덱스 변수는 코드만 지저분하게 할 뿐 꼭 필요한 것은 원소들뿐이다.
- 반복자와 인덱스 변수를 잘못 사용할 가능성이 높아진다.
- 컬렉션이나 배열이냐에 따라 코드 형태가 달라진다.

# for-each

향상된 for문 (Enhanced for statement) 이라는 의미이다.

```java
for (Element e : elements) {
    ...
}
```

- 반복자와 인덱스 변수를 사용하지 않는다.
- 하나의 관용구로 컬렉션, 배열,  **Iterable 인터페이스**를 구현한 객체까지 모두 처리할 수 있다.
- 간단하게 반복을 구성할 수 있고 최적화된 속도를 제공한다.

## Iterable 인터페이스

`Iterable` 인터페이스를 구현한 모든 객체는 `for-each` 문으로 순회할 수 있다.

```java
public interface Iterable<E> {
    // 이 객체의 원소들을 순회하는 반복자를 반환한다.
    Iterator<E> iterator();
}
```

## 컬렉션 중첩 순회

```java
enum Type {HEART, DIAMOND, CLUB, SPADE}
enum Rank {ACE, ONE, TWO, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE,TEN, JACK, QUEEN,KING}

static Collection<Type> types = Arrays.asList(Type.values());
static Collection<Rank> ranks = Arrays.asList(Rank.values());

List<Card> deck = new ArrayList<>();

// types에서 next메서드가 너무 많이 호출된다.      
for (Iterator<Type> i = types.iterator(); i.hasNext(); ) {
    for (Iterator<Rank> j = ranks.iterator(); j.hasNext(); ) {
        deck.add(new Card(i.next(), j.next()));
    }
}
```

마지막 줄의 `i.next()`가 Type 하나당 한번씩 호출되어야 하지만 **Rank 하나당 한번씩 호출**되어 `NoSuchElementException` 예외가 발생한다.

```java
enum Face {ONE, TWO, THREE, FOUR, FIVE, SIX}

Collection<Face> faces = EnumSet.allOf(Face.class);

for(Iterator<Face> i = faces.iterator(); i.hasNext(); ) {
    for (Iterator<Face> j = faces.iterator(); j.hasNext(); ) {
        System.out.println(i.next() + ", " + j.next());
    }
}
```

위와 달리 enum 의 상수 개수가 같다면 **예외가 발생하지 않고 원하는 결과가 제대로 출력되지 않을 것**이다.

```java
// for문을 통한 해결
for(Iterator<Face> i = faces.iterator(); i.hasNext(); ) {
    Face face = i.next(); // 바깥 반복문에 바깥 원소를 저장하는 변수를 추가
    for (Iterator<Face> j = faces.iterator(); j.hasNext(); ) {
        System.out.println(face + ", " + j.next());
    }
}

// for-each문을 통한 해결
for (Face firstFace : faces) {
    for (Face secondFace : faces) {
        System.out.println(firstFace + ", " + secondFace);
    }
}
```

for-each문을 중첩하는 것으로 더욱 간단하게 해결 가능하며 코드도 간결해진다.

## for-each문을 사용할 수 없는 경우

### 파괴적인 필터링

컬렉션을 순회하면서 선택된 원소를 제거해야 하는 경우, for-each 대신 반복자의 `remove` 메서드를 호출해야 한다. <br>
→ 자바8부터 Collection의 `removeIf` 메서드를 사용해 컬렉션을 명시적으로 순회하는 일을 피할 수 있다

```java
// remove 사용
for (CardNo cardNo : cardNos) {
    if (cardNo.number == 1) {
        cardNos.remove(cardNo);
    }
}

// removeIf 사용
cardNos.removeIf(cardNo -> cardNo.number == 1);
```

### 변형

순회하면서 특정 원소의 값 혹은 전체를 교체해야 한다면 리스트의 반복자나 배열의 인덱스를 사용해야 한다.

```java
// for-each : 변경이 되지 않음
for (CardNo cardNo : cardNos) {
    if (cardNo.number == 1) {
        cardNo = new CardNo(10);
    }
}

// for : 변경됨
for (int i = 0; i < cardNos.size(); i++) {
    if (cardNos.get(i).number == 1) {
        cardNos.set(i, new CardNo(10));
    }
}
```

### 병렬 반복

여러 컬렉션을 병렬로 순회해야 한다면 각각의 반복자와 인덱스 변수를 사용해 **엄격하고 명시적으로 제어**해야 한다.

```java
List<Integer> one = Arrays.asList(1, 2, 3);
List<Integer> two = Arrays.asList(10, 20, 30);

for (Iterator<Integer> o = one.iterator(), t = two.iterator(); o.hasNext() && t.hasNext(); ) {
   System.out.printf("one %d : two %d%n", o.next(), t.next());
}
```

## 핵심 정리

전통적인 for문과 비교했을 때 for-each문은 명료하고, 유연하고, 버그를 예방해준다. 

성능 저하도 없으므로 가능한 모든 곳에서 for 문이 아닌 for-each 문을 사용하자.