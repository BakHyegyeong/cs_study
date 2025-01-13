# 아이템 55 : 옵셔널 반환은 신중히 하라

# 값을 반환할 수 없을 때

**자바 8 이전**

1. 예외를 던진다.
    
    → 예외는 정말 예외인 상황에서만 사용해야 하고 예외를 생성할 때 스택 추적 전체를 캡처하므로 비용도 생긴다.
    
2. null을 반환한다.
    
    → 별도의 null 처리 코드를 추가해야 한다
    

위의 두 방법 모두 문제가 존재한다.

**자바 8 이후**

`Optional<T>`를 사용할 수 있게 되었으며 옵셔널은 원소를 최대 1개 가질 수 있는 **불변 컬렉션**이다.

→ `null` 이 아닌 T타입 참조를 담거나, 혹은 아무것도 담지 않을 수 있다.

→ 옵셔널을 반환하는 메서드는 **예외를 던지는 메서드보다 유연**하고 **사용하기 쉬우며** null을 반환하는 메서드보다 **오류 가능성이 작아진다.**


> ⭐ 불변 컬렉션은 실제 collection을 구현한 것은 아니다.


## Optional 사용법

- 변경 전
    
    ```java
    public static String findSomething(List<String> list) {
        if (list.isEmpty()) {
            return null;
        }
    
        for (String s : list) {
            if (s.equals("something")) {
                return s;
            }
        }
        return null;
    }
    ```
    

- 변경 후
    
    ```java
    public static Optional<String> findSomething(List<String> list) {
        if (list.isEmpty()) {
            return Optional.empty();
        }
    
        for (String s : list) {
            if (s.equals("something")) {
                return Optional.of(s);
            }
        }
        return Optional.empty();
    }
    ```
    
    - `Optional.empty()` : 빈 옵셔널 반환
    - `Optional.of(value)` : 값이 든 옵셔널 반환
        
        → null 값이 들어가면 `NullPointerException` 을 던진다.
        
        → **null 값을 넣는 것은 옵셔널을 사용한 목적에 맞지 않는다.**
        
    - `Optional.ofNullable(value)` : null 값도 허용하는 옵셔널


>⭐ 옵셔널은 반환값이 없을 수도 있음을 사용자에게 명확히 알려준다. <br>
→ 옵셔널은 검사 예외(`checkedExeption`)와 취지가 비슷하다.


## Optional 활용 예시

### 1. orElse : 기본값을 설정해둘 수 있다.

```java
String find1 = something.orElse("NONE");
```

### 2. **orElseThrow : 원하는 예외를 던질 수 있다.**

```java
String find2 = something.orElseThrow(IllegalArgumentException::new);
```

### 3. get : 항상 값이 채워져 있다고 가정할 수 있다.

```java
String find3 = something.get();
```

### 4. **orElseGet**

`orElse`는 Optional의 값이 null이 아니더라도 기본값이 생성되는 단점이 있다.
→ 비용이 큰 값을 설정하게 된다면 부담이 될 수 있다.

→ `orElseGet`은 값의 여부에 따라 기본 값을 생성하지 않을 수도 있다.

```java
String find2 = something.orElseGet(() -> nothing());
```

### 5. isPresent() : 값이 있다면 true, 없다면 false

`orElse()`로 대체가 가능하므로 단독으로 사용하지 않는 것이 권장된다.

```java
if (findSomething(List.of("something")).isPresent()) {
    System.out.println("찾았다.");
} else {
    System.out.println("못 찾았다.");
}
```

## Stream과 Optional

자바 9부터 Optional에 `stream()` 메서드가 추가되었는데 Optional을 Stream으로 변환해주는 어댑터 역할을 하는 메서드이다.

**→ 값이 있다면 값을 담은 스트림으로, 값이 없다면 빈 스트림으로 변환**한다.

```java
// 기본 사용법
streamOfOptionals
  .filter(Optional::isPresent)  // 옵셔널에 값이 있다면 
  .map(Optional::get)    // 값을 꺼내 스트림에 매핑
  
// stream() 메서드
streamOfOptionals
  .flatMap(Optional::stream) 
  // 스트림 원소 각각을 하나의 스트림으로 매핑한 다음, 
  // 그 스트림들을 다시 하나의 스트림으로 합치는 메소드
```

## Optional 주의사항

1. 컬렉션, 스트림, 배열, 옵셔널과 같은 컨테이너 타입은 옵셔널로 감싸면 안된다.
    
    → 컨테이너의 경우 **빈 컨테이너를 반환**하는 것이 더 좋을 수 있다.
    
    ```java
    Optional<List<T>>   // X
    List<T>             // O
    ```
    

1. 결과가 없을 수 있으며, 클라이언트가 이 상황을 특별하게 처리해야 하는 경우라면 `Optional<T>` 를 반환한다.

1. 박싱된 기본 타입(Integer, Double, Long)을 담은 옵셔널을 반환하지 말아야 한다.
    
    → 박싱된 기본 타입은 값을 두번이나 감싸기 때문에 **기본 타입보다 무거울 수 밖에 없다.**
    
    → 기본 타입을 옵셔널로 감싼다면 **전용 클래스들인 `OptionalInt`, `OptionalLong`, `OptionalDouble`을 사용하는 것이 좋다.**
    
2. 옵셔널을 키,값,원소나 배열의 원소로 사용하면 절대 안된다.

## 핵심 정리

값을 반환하지 못할 가능성이 있고, 호출할 때마다 반환값이 없을 가능성을 염두에 둬야 하는 메서드라면 옵셔널을 반환하자. 

하지만 옵셔널 반환에는 성능 저하가 뒤따르니, 성능에 민감한 메서드라면 null을 반환하거나 예외를 던지는게 나을 수도 있다. 

또한, 옵셔널을 반환값 이외에 용도로 쓰는 경우는 매우 드물다.