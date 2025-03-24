# 아이템 88 : readObject 메서드는 방어적으로 작성하라

```java
public final class Period {
    private final Date start;
    private final Date end;

    /**
     * @param  start 시작 시각
     * @param  end 종료 시각; 시작 시각보다 뒤여야 한다.
     * @throws IllegalArgumentException 시작 시각이 종료 시각보다 늦을 때 발생한다.
     * @throws NullPointerException start나 end가 null이면 발생한다.
     */
    public Period(Date start, Date end) {
				// 가변인 Date 클래스의 위험을 막기 위해 방어적 복사 진행
        this.start = new Date(start.getTime()); 
        this.end = new Date(end.getTime());

        if (this.start.compareTo(this.end) > 0) {
            throw new IllegalArgumentException(start + " after " + end);
        }
    }

    public Date start() { return new Date(start.getTime()); }
    public Date end() { return new Date(end.getTime()); }
    public String toString() { return start + " - " + end; }
    
    // ... 나머지 코드는 생략
}
```

위의 클래스를 직렬화하기 위해 implements Serializable 만 추가하게 되면 **불변식을 보장할 수 없다.** 

→ **시작 날짜가 끝나는 날짜보다 더 미래인 클래스의 불변식을 깨트리는 객체**를 생성할 수 있다.

→ readObject 메서드가 매개변수로 Byte stream을 받는 또 다른 **public 생성자**이기 때문이다.

→ readObject메서드에 불변식을 깨뜨릴 의도로 임의로 생성한 바이트 스트림을 건네면 문제가 발생한다.

→ 그렇기에 readObject 메서드에서도 **인수가 유효한지 검사해야 하고 필요하다면 매개변수를 방어적으로 복사**해야 한다.

## **유효성 검사를 수행하는 readObject 메서드**

```java
private void readObject(ObjectInputStream s)
        throws IOException, ClassNotFoundException {
    // 불변식을 만족하는지 검사한다.
    if (start.compareTo(end) > 0) {
        throw new InvalidObjectException(start + " after " + end);
    }
}
```

위의 검사로 허용되지 않는 Period 인스턴스 생성을 막을 수 있지만 **가변 공격은 막을 수 없다.**

### 가변 공격

```java
public class MutablePeriod {
    // Period 인스턴스
    public final Period period;

    // 시작 시각 필드 - 외부에서 접근할 수 없어야 한다.
    public final Date start;

    // 종료 시각 필드 - 외부에서 접근할 수 없어야 한다.
    public final Date end;

    public MutablePeriod() {
        try {
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            ObjectOutputStream out = new ObjectOutputStream(bos);

            // 유효한 Period 인스턴스를 직렬화한다.
            out.writeObject(new Period(new Date(), new Date()));

            /*
             * 이전 객체 참조로 내부 Date 필드를 참조한다.
             * 직렬화된 바이트 스트림을 역직렬화하여 Period 인스턴스를 만든다.
             * 그리고 내부의 Date 필드를 훔쳐낸다.
             */
            byte[] ref = {0x71, 0, 0x7e, 0, 5}; // 참조 #5
            bos.write(ref); // 시작 필드
            ref[4] = 4; // 참조 #4
            bos.write(ref); // 종료 필드

            // Period의 내부 Date 필드들을 훔쳐낸다.
            ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(bos.toByteArray()));
            period = (Period) in.readObject();
            start = (Date) in.readObject();
            end = (Date) in.readObject();
        } catch (IOException | ClassNotFoundException e) {
            throw new AssertionError(e);
        }
    }

    public static void main(String[] args) {
        MutablePeriod mp = new MutablePeriod();
        Period p = mp.period;
        Date pEnd = mp.end;

        // 내부의 Date 필드 값을 수정한다.
        pEnd.setYear(78);
        System.out.println(p);

        // 내부의 Date 필드 값을 수정한다.
        pEnd.setYear(69);
        System.out.println(p);
    }
}
```

- 위의 코드에서 Period 인스턴스는 불변식을 유지한 채 생성되었지만 **의도적으로 내부의 값을 수정할 수 있다.**
    
    → byte 스트림의 format만 파악할 수 있다면, 내부 참조를 추가하여 정보를 훔쳐낼 수 있게 된다.
    
- 따라서 readObject에서 불변 클래스 안의 모든 **private 가변 요소를 방어적으로 복사해야 한다.**

## **방어적 복사와 유효성 검사를 수행하는 readObject 메서드**

```java
private void readObject(ObjectInputStream s) throws IOException, ClassNotFoundException {
    s.defaultReadObject();

    // 가변 요소들을 방어적으로 복사한다.
    start = new Date(start.getTime());
    end = new Date(end.getTime());

    // 불변식을 만족하는지 검사한다.
    if (start.compareto(end) > 0) {
        throw new InvalidObjectException(start + " 가 " + end + " 보다 늦다.");
    }
}
```

- 방어적 복사를 유효성 검사보다 앞에 둔다.
- **final 필드는 방어적 복사가 불가능**하므로 한정자를 제거해야 한다.

## 기본 readObject를 사용해도 되는 때

transient 필드를 제외한 모든 필드의 값을 매개변수로 받아 **유효성 검사 없이 필드에 대입하는 public 생성자를 추가**해도 될 경우에는 가능하다.

→ 불가능하다면  커스텀 readObject로 유효성 검사와 방어적 복사를 수행해야 한다.

## 핵심 정리

- private이어야 하는 객체 참조 필드는 각 필드가 가리키는 객체를 방어적으로 복사해야 한다.
    
    → ex) 불변 클래스 내의 가변 요소
    
- 모든 불변식을 검사하여 어긋나는 게 발견되면 InvalidObjectException을 던진다.
- 방어적 복사 다음에는 반드시 불변식 검사가 뒤따라야 한다.
- 역직렬화 후 객체 그래프 전체의 유효성을 검사해야 한다면 ObjectInputValidation 인터페이스를 사용하자.
- 재정의할 수 있는 메서드는 호출하지 말아야 한다.