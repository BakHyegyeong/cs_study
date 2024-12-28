# 아이템 43 : 람다보다는 메서드 참조를 사용하라

# 메서드 참조

메서드를 참조해서 매개변수 리턴 타입을 알아내 람다식의 매개변수를 제거하는 방법

→ 함수 객체를 람다보다 간결하게 만들 수 있다.

```java
//일반 람다식
names = students.stream().map(student -> student.getName()).collect(Collectors.joining(","));
        
//메서드 참조 표현식
names = students.stream().map(Student::getName).collect(Collectors.joining(","));
```

## merge 함수 표현

key, value, 함수를 인자로 받는다.

**key가 map 안에 없다면** value 값을 (key, value) 쌍으로 저장하고

**key가 map 안에 있다면** 인수로 받은 함수를 현재 값과 주어진 값에 적용해 (key, 함수의 결과) 쌍으로 저장한다.

- 람다
    
    ```java
    map.merge(key, 1, (count, incr) -> count + incr);
    ```
    
    위에서 쓰인 람다는 `count + incr`의 값을 반환하기만 하므로 불필요하다. 
    
- 메서드 참조
    
    ```java
    map.merge(key, 1, Integer::sum);
    ```
    
    자바 8에서 도입된 **정적 메서드 sum()의 참조**를 전달해 간결하게 표현할 수 있다.
    

## 메서드 참조 장점

메서드 참조로 구현한다면, **더 짧고 간결한 코드**를 생성할 수 있다.

→ 람다로 구현했을때 너무 길고 복잡하다면,**람다로 작성할 코드를 새로운 메서드에 담은 다음, 람다 대신 그 메서드 참조를 사용**하면 된다.

### 람다가 메서드 참조보다 간결한 경우

메서드와 람다가 같은 클래스에 있는 경우 람다가 더 간결하다.

```java
//메서드 참조 
service.execute(GoshThisClassNameIsHumongous::action);

//람다 사용
service.execute(() -> action());
```

## 메서드 참조 유형

### 정적 메서드 참조

`Integer::parseInt`처럼 정적 메서드를 가리키는 메서드 참조

```java
str -> Integer.parseInt(str)
```

### 인스턴스 메서드 참조

1. 한정적(bound) 인스턴스 메서드 참조 : 수신객체(참조 대상 인스턴스)를 특정하는 참조
    
    → 특정 객체를 기준으로 메서드를 참조한다.
    
    - 사용 방법: `인스턴스::메서드명`
        
        ex) `(String str) -> str.length()` →  `str::length`
        
    
    ```java
    String str = "Hello";
    
    // str 인스턴스의 length() 메서드를 참조
    Supplier<Integer> lengthSupplier = str::length; 
    ```
    

1. 비한정적(unbound) 인스턴스 메서드 참조 : 수신객체를 특정하지 않는 참조
    
    → 수신 객체가 동적으로 주어진다.
    
    - 사용 방법: `클래스명::인스턴스 메서드`
        
        ex) `student -> student.getName()` → `Student::getName`
        
    
    ```java
    List<Student> students = Arrays.asList(new Student("Alice"), new Student("Bob"));
    
    // 비한정적 인스턴스 메서드 참조
    students.forEach(student -> System.out.println(Student::getName)); 
    ```
    
    forEach문에서 **Student 객체가 name이 Alice이거나 Bob인 객체 2개가 올 수 있다는 점**에서 수신 객체가 동적으로 주어진다고 할 수 있다.
    

### 클래스 생성자

클래스 생성자를 가리키는 메서드 참조

`() -> new TreeMap<K,V>()` → `TreeMap<K,V>::new`

### 배열 생성자

배열 생성자를 가리키는 메서드 참조

`len -> new int[len]` → `int[]::new`

| 메서드 참조 유형 | 예 | 같은 기능을 하는 람다 |
| --- | --- | --- |
| 정적 | Integer::parseInt | str -> Integer.parseInt(str) |
| 한정적(인스턴스) | Instant.now()::isAfter | Instant then = Instant.now();t -> then.isAfter(t) |
| 비한정적(인스턴스) | String::toLowercase | str -> str.toLowerCase() |
| 클래스 생성자 | TreeMap<K,V>::new | () -> new TreeMap<K,V>() |
| 배열 생성자 | int[]::new | len -> new int[len] |

## 람다로는 불가능하지만 메서드 참조는 가능한 경우

보통, 람다로 할 수 없는 일이라면 메서드 참조로도 할 수 없다.

하지만, **제네릭 함수 타입**을 구현할때는

**람다 사용이 불가능하고 오직 메서드 참조만 가능**하다.

```java
// 메서드 참조 가능
<E extends Exception> Object m() throws E;

// 람다 불가능
<F extends Exception> () -> String throws F
```

람다식으로는 체크된 예외를 처리하는 것이 JAVA 문법상 불가능하다.

→ 함수형 인터페이스를 위한 제네릭 함수 타입에서 체크된 예외를 처리할 때 메서드 참조 표현식으로는 구현할 수 있지만, 람다식으로는 불가능하다.

## 핵심 정리

메서드 참조는 람다의 간다명료한 대안이 될 수 있다. 메서드 참조 쪽이 짧고 명확하다면 메서드 참조를 쓰고, 그렇지 않을 때만 람다를 사용하라.