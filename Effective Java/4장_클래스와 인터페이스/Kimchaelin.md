## 아이템 15. 클래스와 멤버의 접근 권한을 최소화하라
- 기본 원칙은 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.
- public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.
  - public으로 열 경우 Tread-Safe하지 않다.(Lock 류의 작업을 걸 수 없음)
  - public 필드를 가진 클래스는 데이터 필드에 제약을 못 둔다. -> 외부에서 직접 접근이 가능해져서 필드의 값을 임의로 변경할 수 있다는 것을 의미
- 클래스에서 public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공하면 안 된다.
  - 클라이언트가 해당 배열을 수정할 수 있기 때문이다.
  - 2가지 해결책(100page 참고)
    - public 배열을 private로 만들고 public 불변 리스트를 추가한다.
    - 배열을 private로 만들고 public 메서드를 추가해 배열의 복사본을 반환한다.
- 꼭 필용한 상수라면 예외적으로 public static final로 공개해도 된다. ex) NICKNAME_PATTERN

## 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라
```java
class Point {
    public double x;
    public double y;
}
```
위와 같이 데이터 필드에 직접 접근이 가능하므로, 아래 같이 수정해야 한다.
```java
class Point {
    private double x;
    private double y;

    public Point(double x, double y) {
        this.x = x; 
        this.y = y;
    }
    public double getX() {return x;}
    public double getY() {return y;}
    public void setX(double x) {this.x = x;}
    public void setY(double y) {this.y = y;}
}
```
- 패키지 바깥에서 접근 할 수 있는 클래스라면 접근자를 제공해 데이터를 캡습화 해야 한다.
- package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다 해도 문제가 없다. ->  클래스 수정 범위가 제한되기 때문

## 아이템 17. 변경 가능성을 최소화하라
- 불변 클래스: 그 인스턴스의 내부 값을 수정할 수 없는 클래스
- 클래스를 불변으로 만드는 방법 5가지
  - 객체의 상태를 변경하는 메서드를 제공하지 않는다.
  - 클래스를 확장할 수 없도록 한다. -> 객체의 상태가 변하는 사태를 막음
  - 모든 필드를 final로 선언한다. -> 필드가 final이면 불변이 된다.
  - 모든 필드를 private로 선언한다. -> 외부에서 접근하지 못하게 한다.
  - 가변 컴포넌트에 대해서는 자기 자신만 접근하도록 한다. -> 생성자, 접근자에서 모두 방어적으로 복사를 수행
- 특징
  - 불변 객체는 근본적으로 스레드 안전하여 따로 동기화 할 필요가 없다.
  - 불변 객체는 어떤 스레드도 영향을 줄 수 없으니, 안심하고 공유할 수 있다.
  - 불변 객체는 자유롭게 공유할 수 있으며, 불변 객체끼리는 내부 데이터를 공유할 수 있다.
  - 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다.
    - 값이 변하지 않는 구성요소로 이뤄진 객체가 된다.
  - 불변 객체는 그 자체로 실패 원자성을 제공한다.
    - 생성 시점에 유효성을 검사하고, 객체가 유효하지 않다고 판단되면 객체를 생성하지 않는다.
- 단점
  - 값이 다르면 반드시 독립된 객체로 만들어야 한다.
### 정리 
꼭 필요한 경우가 아니라면 클래스는 불변이어야하며, 불변으로 만들 수 없는 클래스라면 변경할 수 있는 부분을 최소한으로 줄이자.

## 아이템 18. 상속보다는 컴포지션을 사용하라
- 메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.
  - 상위 클래스의 구현에 의존하기 때문에 하위 클래스가 상위 클래스와 강하게 결합된다.
- 컴포지션을 사용하면 상속의 단점을 극복할 수 있다.
  - 컴포지션은 클래스가 다른 클래스의 인스턴스를 참조하는 방식
  - 상속 대신 컴포지션을 사용하면 더 많은 유연성을 제공한다.
    ```java
    // 상속 : HashSet 클래스를 상속받아 기능을 확장
    public class InstrumentedHashSet<E> extends HashSet<E> {
        private int addCount = 0;

        public InstrumentedHashSet() {
        }

        @Override
        public boolean add(E e) {
            addCount++;
            return super.add(e);
        }

        @Override
        public boolean addAll(Collection<? extends E> c) {
            addCount += c.size();
            return super.addAll(c);
        }

        public int getAddCount() {
            return addCount;
        }
    }
    ```
    ```java
    // 컴포지션 :  ForwardingSet 클래스를 상속받고, Set 인터페이스를 구현하는 객체를 포함하여 기능을 확장
    public class InstrumentedSet<E> extends ForwardingSet<E> {
        private int addCount = 0;

        public InstrumentedSet(Set<E> s) {
            super(s);
        }

        @Override
        public boolean add(E e) {
            addCount++;
            return super.add(e);
        }

        @Override
        public boolean addAll(Collection<? extends E> c) {
            addCount += c.size();
            return super.addAll(c);
        }

        public int getAddCount() {
            return addCount;
        }
    }
    ```
    
## 아이템 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라
- 상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용) 문서로 남겨야 한다.
- 효율적인 하위 클래스를 어려움 없이 만들 수 있도록 하기 위해 클래스의 내부 동작 과정 중간에 끼어들 수 있는 hook을 잘 선별하여 protected 메서드로 제공해야 한다.
- 상속용 클래스으로 설계한 클래스는 배포 전 검증해봐야한다.
  - 시험하는 방법은 하위 클래스를 만들어 보는 것이 유일하다.
- 상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.
  - 하위 클래스의 생성자보다 상위 클래스의 생성자가 먼저 호출되어 오작동을 일으킬 수 있다.
- clone과 readObject 모두 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.
  - clone은 하위 클래스의 clone 메서드가 복제본의 상태를 올바른 상태로 수정하기 전에 재정의한 메서드를 호출한다.
  - readObject는 하위 클래스의 상태가 미처 다 역직렬화 되기 전에 재정의 가능 메서드가 호출하게 된다.
- 즉, 상속용으로 설계하지 않은 클래스는 상속을 금지해야 한다.
  - 상속 금지 방법
    - 클래스를 final로 선언하거나 모든 생성자를 private 혹은 package-private로 선언하고 대신 public 정적 팩터리를 제공한다.
    - 상속을 금지하려면 클래스를 final로 선언하면 된다.
