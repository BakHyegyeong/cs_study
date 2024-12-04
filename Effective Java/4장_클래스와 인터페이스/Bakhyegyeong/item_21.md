# 아이템 21 : 인터페이스는 구현하는 쪽을 고려해 설계해라

# Default Method

자바 8 이전에는 기존 구현체를 깨뜨리지 않고는 인터페이스에 메서드를 추가할 방법이 없었지만, 자바 8부터는 **디폴트 메소드** 를 통해 **인터페이스에 메서드를 추가**하는 것이 가능해졌다.

## Default Method 문제점

디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 **디폴트 메서드를 재정의하지 않은 모든 클래스에서 디폴트 구현이 쓰이게 된다.**

→ 기존에 존재하던 구현체들과 연동되지 않을 수 있다.

→ **생각할 수 있는 모든 상황에서 불변식을 해치지 않는 디폴트 메서드를 작성하기란 어렵기 때문이다.**

- 자바 `8` 에서 `Collection` 인터페이스에 추가된 디폴트 메서드

```java
default boolean removeIf(Predicate<? super E> filter) {
	Objects.requireNonNull(filter);
    
    boolean result = false;
    for (Iterator<E> it = iterator(); it.hasNext(); ){
    if(filter.test(it.next()){
    	it.remove();
        result = true;
     }
   }
   return result;
}
```

해당 메서드는 주어진 불리언 함수(`predicate`) 가 참을 반환하는 모든 원소를 제거한다. 

하지만 현존하는 모든 `Collection` 구현체와 잘 어우러지지 않는다.

- 아파치 커먼즈 라이브러리의 `SynchronizedCollection` 클래스

→ 클라이언트가 제공한 **객체로 락을 거는 능력을 추가로 제공**

→ 주어진 락 객체로 모든 메서드에서 동기화 한 후 내부 컬렉션 객체의 기능을 위임하는 래퍼 클래스이기도 하다.

```java
static class SynchronizedCollection<E> implements Collection<E>, Serializable {
        private static final long serialVersionUID = 3053995032091335093L;

        final Collection<E> c;  // Backing Collection
        final Object mutex;     // Object on which to synchronize
        
        SynchronizedCollection(Collection<E> c, Object mutex) {
            this.c = Objects.requireNonNull(c);
            this.mutex = Objects.requireNonNull(mutex);
        }

        public int size() {
            synchronized (mutex) {return c.size();}
        }
        ...
        public boolean contains(Object o) {
            synchronized (mutex) {return c.contains(o);}
        }
  }
```

하지만 `SynchronizedCollection` 클래스는 `removeIf()` 메서드를 재정의하고 있지 않기 때문에, **`removeIf` 메서드는 동기화에 관해 아무것도 알지 못하므로 락 객체를 사용할 수가 없다.**
→ `SynchronizedCollection` 인스턴스를 여러 스레드가 공유하는 환경에서 한 스레드가 `removeIf`를 호출하면 **`ConcurrentModificationException`이 발생하거나 다른 오류가 발생할 가능성**이 있다.

→ 디폴트 메서드는 컴파일에 성공하더라도 기존 구현체에 **런타임 오류**를 일으킬 수 있다.

### 해결 방안

다행히도 `Collection.synchronizedCollection` 이 반환하는 `package-private` 클래스들은 `removeIf` 를 재정의하여, 디폴트 구현을 호출하기 전에 동기화 하도록 설계하였다.

```java
@Override
 public boolean removeIf(Predicate<? super E> filter) {
     synchronized (mutex) {
     	return c.removeIf(filter);
     }
}
```

## 최종 정리

**기존 인터페이스**에 **디폴트 메서드로 새 메서드를 추가하는 일은 꼭 필요한 경우가 아니면 피해야 한다.** 

반면, 새로운 인터페이스를 만드는 경우라면 표준적인 메서드 구현을 제공하는데 유용한 수단이며 인터페이스를 더 쉽게 구현해 활용할 수 있게끔 해준다.