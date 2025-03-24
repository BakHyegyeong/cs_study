# 아이템 90 : 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라

# 직렬화 프록시 패턴

실제 객체를 숨기고 대변인을 통해 직렬화, 역직렬화를 수행하는 방법이다.

→ 디자인 패턴의 프록시 패턴을 응용한 것이라서 장단점도 비슷하다.

## 사용 방법

- 중첩 클래스(직렬화 프록시)를 private static으로 선언한다.
- 생성자는 단 하나여야 한다.
- 바깥 클래스를 매개변수로 받아야 한다.
    
    → 매개변수로 넘어온 인스턴스의 데이터를 복사한다.
    
- 바깥 클래스와 직렬화 프록시 모두 Serializable을 구현한다고 선언해야 한다.

### 바깥 클래스

- writeReplace 메서드
    
    ```java
    private Object writeReplace() {
        return new SerializationProxy(this);
    }
    ```
    
    - 자바의 직렬화 시스템이 바깥 클래스 인스턴스 대신 **SerializationProxy 인스턴스를 반환**하게 한다.
    - 바깥 클래스의 직렬화된 인스턴스를 생성할 수 없다.

- readObject 메서드
    
    ```java
    private void readObject(ObjectInputStream stream) throws InvalidObjectException {
        throw new InvalidObjectException("프록시가 필요합니다.");
    }
    ```
    
    - 역직렬화를 시도할 경우 에러를 반환한다.
    - 바깥 클래스의 불변식을 훼손하려는 공격을 막을 수 있다.

### 직렬화 프록시 클래스

- readResolve 메서드
    
    ```java
    private Object readResolve() {
        return new Period(start, end);
    }
    ```
    
    - 역직렬화 시, 직렬화 프록시를 다시 바깥 클래스 인스턴스로 변환한다
    - 직렬화는 생성자를 사용하지 않고도 인스턴스를 생성하는 기능을 제공한다.
        
        →  하지만 이 메서드에서는 **일반 인스턴스를 만들 때와 똑같은** 생성자, 정적 팩터리, 혹은 다른 메서드를 사용해 **역직렬화된 인스턴스를 생성**한다.
        

### 최종 코드

```java
class Period implements Serializable {
    // 불변 가능
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = start;
        this.end = end;
    }

    // 직렬화 시, Period 인스턴스를 직렬화하지 않고, SerializationProxy 인스턴스를 직렬화한다.
    private static class SerializationProxy implements Serializable {
        private static final long serialVersionUID = 2123123123L; // 아무 값이나 상관없음
        private final Date start;
        private final Date end;

        public SerializationProxy(Period p) {
            this.start = p.start;
            this.end = p.end;
        }

        // Deserialize -> Object 생성 (바깥 클래스와 논리적으로 동일한 인스턴스 반환)
        private Object readResolve() {
            return new Period(start, end);
        }
    }

    // Serialize -> 프록시 인스턴스 반환
    // 결코 바깥 클래스의 직렬화된 인스턴스를 생성해낼 수 없다
    private Object writeReplace() {
        return new SerializationProxy(this);
    }

    // Period 자체의 역직렬화를 방지 -> 역직렬화 시도시, 에러 반환
    private void readObject(ObjectInputStream stream) throws InvalidObjectException {
        throw new InvalidObjectException("프록시가 필요합니다.");
    }
}
```

## **직렬화 프록시 패턴의 장점**

- 방어적 복사처럼 가짜 바이트 스트림 공격과 내부 필드 탈취 공격을 프록시 수준에서 차단한다.
- 직렬화 프록시는 Period의 필드를 final로 선언해도 되므로 Period 클래스를 진정한 불변으로 만들 수도 있다.
- 역직렬화 때 유효성 검사를 수행하지 않아도 된다.
- 역직렬화한 인스턴스와 원래의 직렬화된 인스턴스의 클래스가 달라도 정상 작동한다.

## **직렬화 프록시 패턴의 한계**

- 클라이언트가 멋대로 확장할 수 있는 클래스에는 적용할 수 없다.
- 객체 그래프에 순환이 있는 클래스에는 적용할 수 없다.
    
    → 이런 객체의 메서드를 직렬화 프록시의 readResolve 안에서 호출하려 하면 ClassCastException이 발생한다.
    
    → 직렬화 프록시만 가졌을 뿐 **실제 객체는 아직 만들어진 것이 아니기 때문**이다.
    
- 성능이 느려질 수도 있다.

## 핵심 정리

- 제 3자가 확장할 수 없는 클래스라면 가능한 한 직렬화 프록시 패턴을 사용하자
- 이 패턴이 중요한 불변식을 안정적으로 직렬화해주는 쉬운 방법이다.