# 아이템 50 : 적시에 방어적 복사본을 만들라

# 자바는 안전한 언어이다.

1. 네이티브 메서드를 사용하지 않는다.
    
    → 버퍼 오버런, 배열 오버런, 와일드 포인터 같은 메모리 충돌 오류가 발생하지 않는다.
    
2. 자바의 클래스는 불변식이 지켜진다.
    
    → 불변식 : 클래스의 인스턴스가 특정 조건을 항상 만족해야 한다는 제약조건
    

하지만 클라이언트가 불변식을 깨뜨릴 수 있다는 점을 기억하고 방어적으로 프로그래밍 해야 한다.

## **객체가 자기도 모르게 내부를 수정하도록 허락하는 경우**

: 시작 시간(start)이 종료 시간(end) 보다 빨라야 하는 Period 객체

```java
public final class Period {
	private final Date start;
	private final Date end;

	public Period(Date start, Date end) {
		if (start.compareTo(end) > 0) {
			throw new IllegalArgumentException(
				start + "가 " + end + "보다 늦다."
			);
		}
		this.start = start;
		this.end = end;
	}

	public Date start() {
		return start;
	}

	public Date end() {
		return end;
	}
}
```

## 문제 1 : Date 객체가 가변

```java
Date start = new Date();
Date end = new Date();
Period period = new Period(start, end);
end.setYear(78); // period 내부 변경
```

→ `Date` 대신 불변 인스턴스나 `LocalDateTime/ZondeDateTime` 을 사용하면 방어가 가능하다. <br>
→ Date 는 낡은 API 이니 **새로운 코드를 작성할 때는 더 이상 사용하면 안된다.**

### 방어적 복사를 통한 해결

외부 공격으로부터 클래스 인스턴스의 내부를 보호하려면 **생성자에서 받은 가변 매개변수 각각을 방어적으로 복사하고, 인스턴스 안에서는 원본이 아닌 복사본을 사용해야 한다.**

```java
public Period(Date start, Date end) {
		// 방어적 복사
	  this.start = new Date(start.getTime());
	  this.end = new Date(end.getTime());
	
	  if (this.start.compareTo(this.end) > 0)
	      throw new IllegalArgumentException(
	              this.start + "가 " + this.end + "보다 늦다.");
    }
```

- 매개변수의 **유효성 검사 전 방어적 복사본**을 만들고, **복사본으로 유효성 검사**를 해야 한다.
    
    → 멀티스레드 환경에서 원본 객체의 유효성 검사 후 복사본을 만드는 찰나에 다른 스레드가 원본 객체를 수정할 위험이 있다.
    
    → 검사시점/사용시점(time-of-check/time-of-use) 공격 혹은 TOCTOU 공격이라 한다.
    
- 매개변수가 제 3자에 의해 확장될 수 있는 타입이라면 방어적 복사본을 만들 때 `clone()`을 사용하면 안된다.
    
    → Date는 `final` 이 아니므로 상속이 가능해 `clone()` 의 반환 값이 악의를 가진 하위 클래스의 인스턴스가 될 수도 있기 때문이다.
    
    ```java
    class Date2 extends Date {
    	public Date2() {
    		super();
    	}
    
    	@Override
    	public Object clone() {
    		System.out.println("악의적 오류");
    		return super.clone();
    	}
    }
    ```
    

## 문제 2 : 접근자 메서드

```java
Date start = new Date();
Date end = new Date();
Period period = new Period(start, end);
period.end().setYear(78); // period 내부 변경
```

**게터 메서드와 같은 접근자가 가변 필드의 방어적 복사본을 반환**하면 된다.

```java
public Date start() {
	return new Date(start.getTime);
}

public Date end() {
	return new Date(end.getTime);
}

```

생성자와 달리 접근자 메서드에서는 **방어적 복사에 `clone()` 을 사용해도 된다.** 

→ Period가 가지고 있는 Date 객체는 신뢰할 수 없는 하위 클래스가 아닌 `java.util.Date` 임이 확실하기 때문이다. 하지만 여전히 **생성자나 정적 팩터리를 쓰는 방식이 더 좋다.**

> ⭐  
>  
> **매개변수를 방어적으로 복사하는 목적이 꼭 불변 객체를 만들기 위해서만은 아니다.**  
>  
> → 클라이언트가 제공한 객체의 참조를 내부의 자료구조에 보관해야 할 때면, 항시 그 객체가 **잠재적으로 변경될 수 있는지 생각**해야 한다.  
>  
> → **변경될 수 있는 객체라면** 객체가 클래스로 넘겨진 뒤 임의로 변경되어도 **클래스 자체는 문제없이 동작할지 생각**해야 한다.  
>  
> → 확신할 수 없다면 복사본을 만들어 저장해야 한다.  
>  
> ex) 클라이언트가 건네준 객체를 내부의 **`Set` 인스턴스에 저장**하거나 **`Map` 인스턴스의 키로 사용**할 때  
> → 객체가 변경될 경우 객체를 담고 있는 `Set` 혹은 `Map`의 불변식이 깨질 것이다.  


## 방어적 복사 주의사항

1. 방어적 복사에는 **성능 저하가 따르고, 또 항상 쓸 수 있는 것도 아니다.** 
    
    → 호출자가 컴포넌트 내부를 수정하지 않으리라 확신하면 방어적 복사를 생략할 수 있다. 
    
    → 그래도 수정하지 말아야 함을 명확히 문서화하는 게 좋다.
    
2. **불변 객체를 조합해 객체를 구성**한다면 방어적 복사를 할 일이 줄어든다.
    
    → Period 예제의 경우, 자바 8 이상으로 개발해도 된다면 Instant(LocalDateTime, ZonedDateTime)를 사용하라. 
    
3. 방어적 복사를 생략해도 되는 상황은 한정되어 있다.
    - 해당 클래스와 그 클라이언트가 **상호 신뢰**할 수 있을 때
    - 불변식이 깨지더라도 **그 영향이 오직 호출한 클라이언트**로 국한될 때

## 핵심 정리

클래스가 클라이언트로부터 받는 혹은 클라이언트로 반환하는 구성요소가 **가변이라면 그 요소는 반드시 방어적으로 복사**해야 한다. 

복사 비용이 너무 크거나 클라이언트가 그 요소를 잘못 수정할 일이 없음을 신뢰한다면 방어적 복사를 수행하는 대신 해당 구성요소를 수정했을 때의 **책임이 클라이언트에 의해 있음을 문서에 명시**하자.