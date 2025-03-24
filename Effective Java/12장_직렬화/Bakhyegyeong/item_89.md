# 아이템 89 : 인스턴스 수를 통제해야 한다면 readResolve보다는 열거 타입을 사용하라

# **Singleton 패턴과 직렬화**

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public void leavingTheBuilding() {...}
}
```

위의 클래스는 외부 생성자 호출을 막음으로써 인스턴스가 오직 하나만 만들어짐을 보장하는 싱글턴 패턴이다.

→ 하지만 implements Serializable을 추가하는 순간 싱글턴이 아니게 된다.

→ readObject를 사용하더라도 역직렬화 후에는 이 클래스가 초기화될 때 만들어진 인스턴스와는 **별개의 인스턴스를 반환하게 된다.**

## **readResolve 메서드**

readObject가 만들어낸 인스턴스를 다른 객체로 대체하는 메서드이다.

→ 생성된 역직렬화된 객체가 아닌 **기존의 인스턴스를 반환하면 싱글톤 패턴을 유지할 수 있다.**

```java
// 아직 개선의 여지가 있다.
public class Elvis implements Serializable {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() {}
    public static Elvis getInstance() { return INSTANCE; }

    // 인스턴스 통제를 위한 readResolve
    private Object readResolve() {
        return INSTANCE;
    }

```

### 문제점

readResolve를 인스턴스 통제 목적으로 사용한다면 **객체 참조 타입 인스턴스 필드는 모두 transient로 선언**해야 한다. 

→ 클래스가 transient가 아닌 참조 필드를 가지고 있다면 그 필드의 내용은 **readResolve 메서드가 실행되기 전에 역직렬화 된다.**

→ readResolve 메서드 실행 전에 역직렬화된 객체의 참조를 훔쳐 공격당할 수 있다.

## 해결 방법 - 열거 타입

열거 타입은 선언한 상수 외의 다른 객체가 존재하지 않음을 **자바가 보장**해준다.

→ AccessibleObject, setAccessible 과 같은 특권 메서드를 악용할 경우 뚫릴 수 있지만 네이티브 코드 수행 특권을 가로챈 공격자에겐 모든 방어가 무력화되니 논외이다.

```java
public enum Elvis {
    INSTANCE;

    private String[] favoriteSongs = { "Hound Dog", "Heartbreak Hotel" };

    public void printFavorites() {
        System.out.println(Arrays.toString(favoriteSongs));
    }
}
```

## **readResolve 메서드 규칙**

인스턴스 통제를 위해 readResolve를 사용하는 방식이 완전히 쓸모없지는 않다.

→ 컴파일 타임에 어떤 인스턴스들이 있는 지 알 수 없는 상황이라면 **열거 타입으로 표현하는 것이 불가능**하기 때문이다.

- final 클래스라면 readResolve 메서드는 private 이어야 한다.
- final이 아닌 클래스의 경우
    - private 으로 선언하면 하위 클래스에서 사용할 수 없다.
    - public/protected면 반드시 하위 클래스에서 readResolve 메서드를 재정의해야 한다.
        
        → 하위 클래스 인스턴스를 역직렬화할 때 상위 클래스의 인스턴스를 생성해 ClassCastException을 일으킬 수 있다.
        

## 핵심 정리

- 불변식을 지키기 위해 인스턴스를 통제해야 한다면 열거 타입을 사용하자.
- 여의치 않은 상황에서 직렬화와 인스턴스 통제가 모두 필요하다면 readResolve 메서드를 작성해 넣어야 하고, 그 클래스에서 모든 참조 타입 인스턴스 필드를 transient로 선언해야 한다.