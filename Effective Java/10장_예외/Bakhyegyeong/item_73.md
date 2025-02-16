# 아이템 73 : 추상화 수준에 맞는 예외를 던지라

메서드가 **저수준 예외를 처리하지 않고 바깥으로 전파**( `throws` )해버릴 때, 수행하려는 일과 관련 없어 보이는 예외가 튀어나온다

→ 이는 윗 레벨 API를 오염시킬 수 있어 위험하다.

## **예외 번역 (Exception Translation)**

상위 계층에서는 **저수준 예외를 잡아 자신의 추상화 수준에 맞는 예외로 바꿔 던져야 한다.** 

```java
try {
   ... 
} catch (LowerLevelException e) {
   // 추상화 수준에 맞게 번역한다.
   throw new HigherLevelException(...);
}

```

### 예시 - **AbstractSequentialList**

```java
/**
  이 리스트 안의 지정한 위치의 원소를 반환한다.
  @throws IndexOutOfBoundsException index가 범위 밖이라면, 
  즉 {@code index < 0 || index >= size()}이면 발생한다.
**/
public E get(int idex) {
   ListIterator<E> i = listIterator(index);
   try {
      return i.next();
   } catch (NoSuchElementException e) {
   	  throw new IndexOutOfBoundsException("인덱스: " + index);
   }
}
```

### 주의 사항

결론적으로 예외를 전파( `throws` )하는 것보다 예외 번역이 더 나은 방법이지만 남용해서는 안된다.

1. 가능하다면 저수준 메서드가 반드시 성공해 예외를 발생시키지 않도록 한다.
2. 상위 계층 메서드의 매개변수 값을 아래로 넘기기 전에 미리 검사한다.
3. 예외를 예방 및 처리 불가능할때 예외 번역을 사용한다.

만약 아래 계층에서의 예외가 불가피하다면, **상위 계층에서 예외를 조용이 처리하여 API 호출자에게까지 전파하지 않는 방법**도 존재한다. 이 경우, 발생한 예외는 로깅 기능을 활용하여 기록해두는 것이 좋다.

## **예외 연쇄 (Exception Chaining)**

문제의 근본 원인인 저수준 예외를 고수준 예외에 실어 보내는 방식

→ 예외를 번역할 때, 저수준 예외가 디버깅에 도움이 될 때 사용한다.

```java
try {
   ... 
} catch (LowerLevelException cause) {
   // 저수준 예외를 고수준 예외에 실어 보낸다.
   throw new HigherLevelException(cause);
}
```

고수준 예외의 생성자는 상위 클래스의 생성자에 이 '원인'을 건네주어, 최종적으로 Throwable(Throwable) 생성자까지 건네지게 한다.

## 핵심 정리

- 아래 계층의 예외를 예방하거나 스스로 처리할 수 없고, 상위 계층에 그대로 노출하기 곤란하다면 예외 번역을 사용하자.
- 이때 예외 연쇄를 이용하면 상위 계층에는 맥락에 어울리는 고수준 예외를 던지면서, 근본 원인도 함께 알려주어 오류를 분석하기에 좋다.