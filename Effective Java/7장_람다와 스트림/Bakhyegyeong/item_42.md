# 아이템 42 : 익명 클래스보다는 람다를 사용하라

# 함수 객체

함수 타입을 표현할 때, 추상 메서드를 하나만 담은 인터페이스(함수형 인터페이스) 또는 추상 클래스의 인스턴스

→ 특정 함수나 동작을 표현

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

# 익명 클래스

이름이 없는 클래스로 별도의 클래스 선언 없이 코드부에서 바로 구현하는 기술

- 일회성으로 사용하는 클래스의 경우에는 익명 클래스로 클래스를 생성하는 비용을 줄일 수 있다.
- 클래스 선언과 인스턴스화를 동시에 할 수 있다.
- JDK 1.1 이후부터 함수 객체를 만들 때 주로 사용되었다.
- 내부에 생성자 선언이 불가능하다.

```java
Collections.sort(words,new Comparator<String>() {
   public int compare(String s1, String s2) {
      return Integer.compare(s1.length(), s2.length());
   }
});
```

`new Comparator<String>()` 뒤에 `{}`로 바로 구현되고 있다.

## 람다

메서드로 전달할 수 있는 **함수 객체를 단순화**한 것으로 이름을 가지지 않고 **매개변수 리스트, 바디, 반환 형식** 등을 가진다.

→ 익명 클래스보다 간결하게 코드를 작성할 수 있다.

```java
Collections.sort(words,
		(s1,s2) -> Integer.compare(s1.length(), s2.length()));
```

## 람다 사용 비교

```java
public enum Operation {
    PLUS  ("+") {
    	public double apply(double x, double y) {return x + y; }
    },
    MINUS ("-") {
    	public double apply(double x, double y) {return x + y; }
    },  
    ...
}

// 람다 사용
public enum Operation {
    PLUS  ("+", (x, y) -> x + y),
    MINUS ("-", (x, y) -> x - y),
```

## 람다의 타입 추론

람다에서 모든 **매개변수 타입은 생략**한다.

→ 컴파일러가 문맥을 살펴 타입을 추론해주기 때문에 코드에서는 생략해도 된다.

```java
List<Integer> numbers = new ArrayList<>(List.of(3, 2, 1, 4, 10, 5, 7));
numbers.sort((o1, o2) -> Integer.compare(o1, o2));
```

람다의 매개변수 o1, o2의 타입은 `Integer`이고 반환타입은 `int`이다. 하지만 컴파일러가 타입을 추론하기 때문에 **생략**되었다.

→ 컴파일러는 **타입 정보 대부분을 제네릭**에서 얻기 때문에 람다를 사용할 때 제네릭의 중요성이 더 강조된다.

→ 위의 코드에서 List<Integer>가 아닌 List로 로 타입을 사용한다면 컴파일 오류가 발생할 수 있다.

## 람다의 단점

1. 이름이 없고 문서화를 하지 못한다.
    
    따라서 코드가 복잡하거나 길어질 경우 람다는 비효율적일 수 있다.
    
    → 한줄에서 세 줄 안에 끝낼 경우에 효과적이다.
    
2. 람다는 함수형 인터페이스에서만 사용이 가능하다.
    
    → 함수형 인터페이스의 추상 메서드만을 사용하는 것이 아닌 추상 클래스의 인스턴스를 만들고자 할 때는 사용할 수 없다.
    
3. 람다는 자신을 참조할 수 없다.
    
    → this키워드는 바깥의 인스턴스를 가리킨다.
    
4. 람다를 직렬화하는 것은 위험하다.
    
    람다도 익명 클래스처럼 직렬화 형태가 **가상 머신 구현별(JVM)로 다를 수 있으므로**, `Comparator` 처럼 직렬화해야하만 하는 함수 객체는 `private` 정적 중첩 클래스의 인스턴스를 사용하는 것이 좋다.
    
    ```java
    // 람다
    public class LambdaExample {
    
        public static void main(String[] args) {
            Comparator<Integer> comparator = (o1, o2) -> Integer.compare(o1, o2);
        }
    }
    
    // private 정적 중첩 클래스
    public class privateExample {
        private static class ComparatorImpl implements Comparator<String>, Serializable {
            @Override
            public int compare(String o1, String o2) {
                return o1.length() - o2.length();
            }
        }
    
        public static void main(String[] args) throws IOException, ClassNotFoundException {
            Comparator<String> comparator = new ComparatorImpl();
    ```
    
    private 정적 중첩 클래스는 명확한 클래스 타입을 가지고 있기에 직렬화된 객체를 역직렬화할 때 **클래스의 구조가 명확하게 정의되어 있다.** 
    
    → JVM에 관계없이 일관된 방식으로 직렬화된다.
    
    → 람다 표현식은 JVM에 따라 직렬화의 결과가 달라질 수 있다.
    

    > ⭐ 직렬화 : 객체의 상태를 **바이트 스트림으로 변환**해 **파일에 저장**하거나 **네트워크를 통해 전송**할 수 있도록 하는 과정
    
    

    

## 핵심 정리

익명 클래스는 함수형 인터페이스가 아닌 타입의 인스턴스를 만들 때만 사용하고, 작은 함수 객체를 쉽게 표현할 수 있는 람다를 사용하는 것이 좋다.