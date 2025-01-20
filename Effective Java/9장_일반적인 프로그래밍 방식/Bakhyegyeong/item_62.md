# 아이템 62 : 다른 타입이 적절하다면 문자열 사용을 피하라

# 문자열을 잘못 사용하는 경우

## 문자열로 다른 값 타입을 대신하는 경우

- 데이터가 수치형임에도 불구하고 문자열로 받는 경우
    
    → 숫자면 숫자, 예/아니오라면 `boolean` 과 같은 명확한 타입으로 받는게 더 좋다.
    
- 열거 타입을 문자열로 대신하는 경우
- 혼합 타입을 문자열로 대신하는 경우
- 권한을 문자열로 표기하는 경우

## 혼합 타입을 문자열로 대신하는 경우

여러 요소가 혼합된 데이터를 하나의 문자열로 표현하는 것은 좋지 않다.

```java
String compoundKey = className + "#" + i.next();
```

- 각 요소를 개별로 접근하려 할때 **문자열을 파싱**해야 해서 느리고 오류 가능성도 커진다.
- `equals()`, `toString()`, `compareTo()` 메서드를 사용할 수 없다.

**해결**

```java
public class A {
	private class B {
    ...
    }
}
```

3가지 필드를 보관할 수 있는 **private 정적 멤버 클래스**로 만든다.

## 권한을 문자열로 표기하는 경우

각 스레드가 자신만의 변수를 갖게 해주는 스레드 지역변수 기능을 구현하고자 한다.

### 문자열 키로 스레드별 지역변수를 식별

```java
public class ThreadLocal {
    private ThreadLocal() {} //객체 생성 불가

    // 현 스레드의 값을 키로 구분해 저장한다.
    public static void set(String key, Object value);

    // (키가 가리키는) 현 스레드의 값을 반환한다.
    public static Object get(String key);
}
```

스레드 구분용 문자열 키가 전역 이름 공간 (global namespace)에서 공유되는 문제가 있다.

→ 스레드들이 같은 키를 사용하게 된다면 **의도치 않게 변수를 공유**하게 된다. 

→ 다른 클라이언트가 문자열만 알면 다른 스레드가 사용하는 ThreadLocal 변수를 가져올 수 있는 것이다.

### **Key 클래스로 권한 부여**

```java
public class ThreadLocal {
    private ThreadLocal() {} // 객체 생성 불가

	// 내부의 Key 클래스를 이용해 인스턴스에 무관한 key를 생성
    public static class Key {
        key() {}
    }

    // 위조 불가능한 고유 키를 생성
    public static Key getKey() {
        return new Key();
    }

    public static void set(Key key, Object value);
    public static Object get(Key key);
}
```

- set과 get 메서드를 정적 메서드로 사용하지 않고 **Key의 인스턴스 메서드를 사용**할 수 있다.
- Key는 더 이상 스레드 지역변수를 구분하기 위한 키가 아니라, **그 자체가 스레드 지역변수**가 된다. <br>
→ ThreadLocal은 할 일이 없기 때문에 Key의 이름을 ThreadLocal로 변경한다.

```java
public final class ThreadLocal {
    public ThreadLocal();
    public void set(Object value);
    public Object get();
}
```

[상세 설명](https://jake-seo-dev.tistory.com/544)

## 핵심 정리

더 적합한 데이터 타입이 있거나 새로 작성할 수 있다면, 문자열은 쓰지 말자. 

문자열은 잘못 사용하면 번거롭고, 덜 유연하며 느리고 오류 가능성도 크다.