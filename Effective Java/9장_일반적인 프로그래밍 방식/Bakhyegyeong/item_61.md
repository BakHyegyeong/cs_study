# 아이템 61 : 박싱된 기본 타입보다는 기본 타입을 사용하라

# 자바의 데이터 타입

- 기본 타입 : `int`, `double`, `boolean` ..
- 참조 타입(박싱된 기본 타입) : `Integer`, `Double`, `Boolean` ..

**오토박싱과 오토언박싱 때문에 두 타입이 자동으로 형변환** 되기 때문에 크게 구분하지 않아도 되지만 둘은 큰 차이가 있다.

## **기본 타입과 박싱된 기본 타입의 차이**

- 기본 타입은 값만 가지고 있으나, 박싱된 기본 타입은 식별성(identity)이라는 속성도 갖는다.
    
    → 박싱된 기본 타입의 두 인스턴스는 **값이 같아도 다르다고 식별될 수 있다.**
    
- 기본 타입의 값은 언제나 유효하나, 박싱된 기본 타입은 null을 가질 수 있다.
- 기본 타입이 박싱된 기본 타입보다 메모리 면에서 우수하다.

## 차이점으로 문제가 생기는 경우

### 의도치 않은 식별성 검사

```java
Comparator<Integer> naturalOrder = (i, j) -> (i < j) ? -1 : (i == j ? 0 : 1);
  
naturalOrder.compare(new Integer(42), new Integer(42)) // 1 출력
```

i가 j보다 작다면 -1, i와 j가 같다면 0, i가 j보다 크고 다른 숫자라면 1을 출력한다.

→ 숫자의 값이 같으므로 0을 반환해야 하지만 **1을 반환한다.**

→ `i == j` 에서 **객체 참조의 식별성**을 검사하여 **객체의 주소값을 기준으로 비교**하여 1이라는 결과값이 나오게 된 것이다.

**해결**

```java
Comparator<Ineteger> naturalOrder = (iBoxed, jBoxed) -> {
	int i = iBoxed, j = jBoxed;  // 오토 박싱
    return i < j ? -1 : (i == j ? 0 : 1);
}
```

오토박싱으로 검사 전에 바꿔서 **식별성 검사가 이루어지지 않게** 한다.

→ 또는 `Integer.compare()` 를 사용한다.

### 기본 타입에서의 NullPointerException

```java
public cass Unbelievable {
	static Integer i;  // 예외 발생!
    
    public static void main(String[] args){
    	if (i == 42)
        	System.out.println("믿을 수 없군!");
    }
}
```

컴파일 타임에는 에러가 발생하지 않지만 실행시키는 순간 `NullPointerException`이 발생한다.

→ 내부적으로 박싱 타입인 **Integer 객체와 기본 타입인 int를 비교**하게 되고 Integer 객체의 **초기값은 null**이기 때문에 예외가 발생한다.

**해결**

```java
static int i
```

i를 기본타입인 `int`로 선언한다.

→ int의 경우에는 0으로 자동으로 초기화되기 때문에 예외가 발생하지 않는다.

### 의도치 않은 속도 저하

```java
public static void main(String[] args) {
	Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++) {
    	sum += i;
    }
    System.out.println(sum);
}
```

sum을 박싱된 기본 타입으로 선언해 **내부적으로 박싱과 언박싱이 반복**되고 이 때문에 매우 느린 속도를 보여준다.

**해결**

```java
long sum = 0L;
```

sum을 기본 타입인 `long`으로 선언한다.

## **박싱된 기본 타입의 용도**

- 컬렉션의 원소, 키, 값
- 매개변수화 타입이나 매개변수화 메서드의 타입 매개변수
- 리플렉션을 통해 메서드를 호출할 때

## 핵심 정리

- 기본 타입과 박싱된 기본 타입 중 하나를 선택해야 한다면 **가능한 기본 타입**을 사용하자.
- 박싱된 기본 타입을 써야 한다면, 다음의 세가지를 꼭 기억하자.
    - 박싱된 기본 타입을 `==`로 비교하면 식별성 검사가 이루어진다.
    - 박싱된 기본 타입에서 기본 타입으로 언박싱하는 과정에서NullPointerException 이 발생할 수 있다.
    - 기본 타입을 박싱하는 작업은 필요 없는 객체를 생성하는 부작용이 있다.