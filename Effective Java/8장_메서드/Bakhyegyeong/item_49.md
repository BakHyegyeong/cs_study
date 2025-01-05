# 아이템 49 : 매개변수가 유효한지 검사하라

# 메서드의 매개변수

대부분의 메서드와 생성자는 매개변수의 값이 특정 조건을 만족해야 한다. 

→ 메서드 몸체가 시작되기 전에 매개변수를 검사하는 것이 좋다.

매개변수 검사가 제대로 하지 못하면 아래와 같은 원자성을 어기는 문제가 발생한다.

- 메서드가 수행되는 중간에 예외를 던지며 실패
- 메서드는 잘 수행되지만 잘못된 결과를 반환
- 메서드는 잘 수행되지만 객체를 이상한 상태로 만들어 놓아서 미래에 메서드와는 관련 없는 오류를 발생시킴

## 매개변수를 검사하는 방법 - public, protected

### @throws

public과 protected 메서드는 매개변수 값이 잘못됐을 때 던지는 예외를 **`@throws` 자바독 태그를 활용해 문서화**해야 한다.

```java
/**
(현재 값 mod m)을 반환한다. 
...
@param m 계수(양수여야 한다.)
@return 현재 값 Mod m
@thorws ArithmeticException m이 0보다 작거나 같으면 발생한다.
*/
public BigInteger mod(BigInteger m){
	if (m.signum() <= 0){
    	throw new ArithmeticException("계수(m)는 양수여야 합니다. " + m);
    ...
}
```

발생하는 예외는 보통 `IllegalArgumentException`, 
`IndexOutOfBoundsException`, `NullPointerException` 중 하나가 될 것이다

### **null 검사시 requireNonNull**

`@Nullable` 이나 비슷한 어노테이션을 사용하여 특정 매개변수는 null이 될 수 있다고 알려줄 수 있지만, 표준적인 방법은 아니다.

→ 자바 7에서 추가된 `Objects.requireNonNull` 메서드를 사용한다면 더 이상 null 검사를 수동으로 하지 않아도 된다.

```java
this.strategy = Objects.requireNonNull(strategy, "전략");
```

원하는 예외 메시지도 지정할 수 있고 입력을 그대로 반환해 값을 사용하는 동시에 null 검사를 수행할 수 있다.

### Objects 범위 검사

자바 9부터는 `checkFromIndexSize()`, `checkFromToIndex()`, `checkIndex()` 와 같은 메서드들이 추가되면서 Objects에 범위 검사 기능도 더해졌다. <br>
→ 예외 메시지를 지정할 수 없으며 리스트와 배열 전용으로 설계되었다. <br>
→ 특정 범위의 유효성을 검사할 때 닫힌 범위(마지막 인덱스)는 다루지 못한다는 단점이 존재한다.

```java
List<String> list = Arrays.asList("A", "B", "C", "D", "E");
int fromIndex = 0;
int toIndex = 5; // 리스트의 크기

// checkFromToIndex()의 경우
if (fromIndex < 0 || toIndex > list.size() || fromIndex > toIndex) {
    throw new IndexOutOfBoundsException("인덱스 범위가 유효하지 않습니다.");
}
// 리스트의 크기인 5는 유효하지 않은 인덱스로 판단한다.
```

## 매개변수를 검사하는 방법 - private

공개되지 않은 메서드라면, 패키지 제작자가 직접 메서드가 호출되는 상황을 통제할 수 있다. 

→ 오직 유효한 값만이 메서드에 넘겨지리라는 것을 보증해야 한다.

### assert

public 이 아닌 메서드라면 단언문(assert)를 사용해 매개변수 유효성을 검사할 수 있다.

```java
private static void sort(long a[], int offset, int length){
	assert a != null;
  assert offset >= 0 && offset <= a.length;
  assert length >= 0 && length <= a.length - offset;
  ... // 계산 수행
}
```

- 메서드가 직접 사용하지는 않으나 나중에 쓰기 위해 저장하는 매개변수는 더 신경 써서 검사해야 한다.
    
    → 나중에 추적하기 어려워 디버깅이 상당히 괴로워질 수 있다. 
    
- 생성자 매개변수의 유효성 검사는 클래스 불변식을 어기는 객체가 만들어지지 않게 하는데 꼭 필요하다.

> ⭐ **단언문(assert) 특징**  
>  
> - 실패 시 `AssertionError`를 던진다.  
> - 런타임에 아무런 효과도, 아무런 성능 저하도 없다.  


## 예외

메서드 몸체 실행 전에 매개변수 유효성을 검사해야 한다는 규칙에도 예외는 존재한다. 

- 유효성 검사 비용이 지나치게 높거나 실용적이지 않을 때
- 계산 과정에서 암묵적으로 검사가 수행될 때

    ex) `Collections.sort(List)` : 객체 리스트를 정렬하는 메서드

  - 리스트 안의 객체들은 모두 상호 비교될 수 있어야 하며, **정렬 과정에서 이 비교가 이뤄진다.**
  - 비교될 수 없는 객체가 들어 있다면 비교할 때 `ClassCastException`을 던진다.
      
      → 비교하기 앞서 모든 객체가 상호 비교될 수 있는지 검사해봐야 별다른 실익이 없다. 
    

## 핵심 정리

메서드나 생성자를 작성할 때는 매개변수들에 어떠한 제약이 있을 지 생각해야 한다. 

그 제약들을 문서화하고 메서드 코드 시작 부분에서 명시적으로 검사하면, 유효성 검사가 실제 오류를 처음 걸러낼 때 효과를 볼 것이다.