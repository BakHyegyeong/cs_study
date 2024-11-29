# 아이템 18 : 상속보다는 조합(컴포지션)을 사용하라

## 상속의 문제점

같은 패키지의 부모 클래스를 확장하는 상속은 코드를 재사용하는 강력한 수단이지만, **다른 패키지의 클래스를 상속하는 일은 위험**하다.

→ 메서드 호출과 달리 상속은 **캡슐화를 깨뜨릴 수** 있기 때문이다.

→ 캡슐화가 깨짐으로써 하위 클래스는 **상위 클래스에 강하게 결합, 의존**하게 되고 이로 인해 **변화에 유연하게 대처하기 힘들어진다.**

> 💡 상위 클래스와 하위 클래스의 관계가 **컴파일 시점에 결정**되어 구현에 의존하기 때문에 **실행시점에 객체의 종류를 변경하는 것이 불가**능하며 **다형성과 같은 객체지향의 이점을 활용할 수 없다.**


## 하위 클래스가 깨지는 경우

**확장을 충분히 고려하지 않고 문서화도 해놓지 않은**, 즉 릴리스마다 **내부 구현이 달라질 수 있는 상위 클래스를 상속받았을 경우 하위 클래스가 오동작**할 수 있다.

### 자기 사용의 경우

아래는 HashSet을 상속한 클래스이다.

여기서 HashSet의 addAll 메서드를 오버라이딩했다.

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;  // 추가된 원소 개수

    ...
    
    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        // addCount의 값만 늘리고
        return super.addAll(c);
        // 실제로 리스트를 추가하는 것은 부모 클래스의 addAll()
    }

    public int getAddCount() {
        return addCount;
    }
}
```

그리고 아래와 같이 인스턴스에 원소 3개를 addAll메서드를 이용해 추가하였다.

```java
public static void main(String[] args) {
        InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
        s.addAll(List.of("틱", "탁탁", "펑"));
        System.out.println(s.getAddCount()); // 6이 반환?
}
```

이때 getAddCount()의 결과는 3이 아닌 6이 반환된다.

원인은 HashSet의 addAll메서드에 있다.

```java
public boolean addAll(Collection<? extends E> c){
	boolean modified = false;
	for (E e : c)
		if(add(e))
			modified = true;
	return modified;
}
```

addAll 메서드 안에서 다시 add메서드를 호출하면서 자식 클래스의 add메서드가 호출된다.

그리고 add메서드 안의 addCount값을 증가시키는 로직때문에 카운팅이 중복으로 발생하였다.

- 내부의 이런 구현 방식이 문서에 명시되어 있지 않았기에 문제가 발생하였다.
- **내부에서 같은 클래스의 다른 메서드를 사용하는 자기사용** 여부는 해당 클래스의 내부 구현 방식에만 해당하기 때문에, 다음 릴리즈에서도 변경이 일어나면서 하위 클래스가 깨져버릴 가능성이 높다.

### 보안 관련 문제

다음 릴리스에서 상위 클래스에 새로운 메서드를 추가하는 경우, 보안 때문에 컬렉션에 추가된 모든 원소가 특정 조건을 만족해야만 한다 가정하자.

하지만 컬렉션을 상속하여 원소를 추가하는 모든 메서드를 재정의해 필요한 조건을 먼저 검사하게 하는 방식은, 상위 클래스에 또 다른 원소 추가 메서드가 만들어지기 전까지만 유효하다.

하위 클래스에서 재정의하지 못한 새로운 메서드를 이용해 '허용되지 않은' 원소를 추가할 수 있게 되기 때문이다.

→ 즉 상위 클래스에서 변경된 부분을 하위 클래스에서 즉시 캐치하고 변경하지 않는 이상 항상 문제가 생길 수 있다.

## 컴포지션을 활용하자

### 컴포지션

**기존 클래스를 확장하지 않고**, **새롭게 만든 클래스 내부 private 필드로 기존 클래스의 인스턴스를 참조**하는 방식

→ 기존 클래스가 새로운 클래스의 구성요소로 사용된다.

### 전달 메서드 활용

```java
public class WinnginLotto {
    private Lotto lotto;
    // 상위 클래스 인스턴스
    private LottoNumber bonusBall;

    public long containsLotto(Lotto otherLotto) {
        return lotto.calculateMatchCount(otherLotto);
		    // 상위 클래스 인스턴스를 통해 메서드를 호출
    }  
    ... 
}
```

`WinningLotto` 클래스는 `Lotto` 클래스의 메서드를 호출해 그 결과를 반환하는 방식으로 작동한다.

이 방식을 **전달(forwarding)** 이라고 하며, `WinningLotto` 클래스의 메서드들을 **전달 메서드**라고 부른다.

### 전달 클래스 활용

부가 기능이 존재하는 래퍼 클래스(`InstrumentedSet`)와 실제 상위 클래스(`Set` ) 사이에 중간 계층(`ForwardingSet`)을 하나 두어, 래퍼 클래스에서 실제 상위 클래스의 메서드가 호출되어도 **상속 관계가 아닌 단순히 인자로 받고 호출된 것이므로 재정의에 전혀 영향을 받지 않는다.**

- 중간 계층 : ForwardingSet

```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;    
    // 내부 필드로 상위 클래스의 참조를 지님

    public ForwardingSet(Set<E> s) { this.s = s; }

    ...
    public boolean add(E e)           { return s.add(e); }      }
    
    public boolean addAll(Collection<? extends E> c)
                                   { return s.addAll(c);      }
    ...
}
```

기존의 부모 클래스에서 내부 필드로 상위 클래스를 참조한다.

- 부가기능이 존재하는 하위 클래스 : InstrumentedSet

```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
        // 중간 계층을 상속받아 상위 클래스 Set과의 직접적인 연결을 피했다.
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
        // 이 add는 ForwardingSet의 add를 호출 후
        // 그 안에서 다시 Set의 add를 호출해 수행한다.
    }
    
    @Override 
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    
    public int getAddCount() {
        return addCount;
    }

    public static void main(String[] args) {
        InstrumentedSet<String> s = new InstrumentedSet<>(new HashSet<>());
        s.addAll(List.of("틱", "탁탁", "펑"));
        System.out.println(s.getAddCount()); // 3
    }
}
```

## 컴포지션 장점

1. **기존 클래스의 내부 구현 방식과 독립**적이게 되서, 메서드 재정의에 따른 부가 영향을 고려하지 않아도 되어 안전하다.
2. 재사용할 수 있는 **전달 클래스**를 하나만 만들어두어도, 원하는 기능을 덧씌우는 클래스들을 손쉽게 구현할 수 있다. 
    
    → 상속을 사용했다면, 매번 부모 클래스를 상속 받은 새로운 구체 클래스를 생성했어야 할 것이다.
    

## 컴포지션 단점

콜백(Callback) 프레임워크와는 어울리지 않는다.

> 💡 콜백 프레임워크 <br>
자기 자신의 참조를 다른 객체에 넘겨서, 다음 호출(콜백) 때 사용하도록 하는 방식


## 컴포지션 대신 상속을 사용할 때 주의사항

1. 확장을 고려하고 설계한 확실한 is-a 관계일 때
    
    ```java
    public class 포유류 extends 동물 {
    
        protected void 숨을_쉬다() {
            ...
        }
    
        protected void 새끼를_낳다() {
            ...
        }
    }
    ```
    

1. API에 아무런 결함이 없는 경우, 결함이 있다면 하위 클래스까지 전파되어도 괜찮을 때

## 최종 정리

상속은 강력하지만 캡슐화를 해친다는 문제가 있다. 상속은 상위 클래스와 하위 클래스가 순수한 is-a 관계일 때만 써야 하는데, 여전히 하위 클래스의 패키지가 상위 클래스와 다르고, 상위 클래스가 확장을 고려해 설계되지 않았다면 문제가 될 수 있다. 상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용하자.