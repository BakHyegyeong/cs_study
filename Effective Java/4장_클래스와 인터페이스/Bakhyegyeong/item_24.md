# 아이템 24 : 멤버 클래스는 되도록 static 으로 만들어라

# 중첩 클래스

**다른 클래스 안에 정의**되어 **자신을 감싼 바깥 클래스에서만** 쓰여야 하는 클래스

→ 불필요한 노출을 줄여 **캡슐화**를 할 수 있고 **가독성 좋고 유지보수하기 좋은 코드**를 작성할 수 있다.

- 정적 멤버 클래스
- 비정적 멤버 클래스
- 익명 클래스
- 지역 클래스

정적 멤버 클래스를 제외한 클래스들은 내부 클래스(inner class)라고 한다.

## 정적 멤버 클래스

- class 내부에 static으로 선언된 클래스 <br>
→ `static` 키워드가 함께 작성되었는지 여부에 따라 정적 멤버 클래스인지 비정적 멤버 클래스인지 구분할 수 있다.
- 바깥 클래스의 private 멤버에도 접근 가능

→ 위의 2가지 특징을 제외하고 일반 클래스와 똑같다.

- private로 선언 시 바깥 클래스에서만 접근 가능

```java
class OuterClass {
    private int a = 100;
		
		// static으로 작성
    static class InnerClass {
        private int b;

        void accessOuterClass(){
            OuterClass outerClass = new OuterClass();
            outerClass.a = 1;
        }
    }
}

// 정적 멤버 클래스의 인스턴스 생성
public void createClass(){
    OuterClass.InnerClass innerClass = new InnerClass();
}
```

### 주의사항

1. **비정적 멤버 클래스**의 인스턴스 메서드에서 `this` 를 사용하여 **바깥 인스턴스의 참조**를 가져올 수 있다. <br>
→ 비정적 멤버 클래스는 숨은 바깥 클래스의 인스턴스 참조를 가지고 있게 되고, 이는 **가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하지 못해 메모리 누수**가 생길 수 있다.
2. 바깥 클래스는 로드 없이 **내부 클래스만 독립적으로 로드**된다.

중첩 클래스의 인스턴스가 바깥 인스턴스와 **독립적으로 존재**할 수 있다면, **바깥 클래스가 표현하는 객체**의 한 부분(구성요소)일 때 사용한다.

## 비정적 멤버 클래스

- static이 붙지 않은 멤버 클래스
- 비정적 멤버 클래스의 인스턴스 메서드에서 `클래스명.this`를 사용해 바깥 인스턴스의 메서드나 참조 가져올 수 있음
- **바깥 인스턴스 없이는 생성 불가**
    
    → 비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적인 연결
    

```java
class OuterClass {
    private int a = 100;

		// static이 없다.
    class InnerClass {
        private int b;
        
        public void callTestClassMethod() {
            // 바깥 클래스의 메서드 호출하기
            OuterClass.this.testMethod();
        }
    }
    
    // 비정적 멤버 클래스 생성 후 반환
    public InnerClass createInnerClass() {
        return new InnerClass();
    }
    
    // 바깥 클래스의 메서드
    public void testMethod() {
        System.out.println("hello world");
    }
}
```

- 비정적 멤버 클래스 생성 방법 : 이 방법으로 **바깥 클래스와 비정적 멤버 클래스의 관계가 생성**된다.
    - 바깥 클래스의 인스턴스 메서드에서 비정적 멤버 클래스의 생성자 호출
    - `바깥 클래스 인스턴스.new 멤버클래스()` 호출
    
    ```java
    OuterClass test = new OuterClass();
    InnerClass publicSample1 = test.createInnerClass(); // 바깥 클래스의 인스턴스 메서드에서 생성자 호출
    InnerClass publicSample2 = test.new InnerClass(); // 바깥 인스턴스 클래스.new 멤버클래스()
    ```
    

### 주 사용처

**멤버 클래스에서 바깥 인스턴스에 접근해야 하는 어댑터를 정의**할 때 자주 쓰인다.

> ⭐ 어댑터 : 특정 클래스의 인스턴스를 감싸 마치 **다른 클래스의 인스턴스처럼 보이게 하는 뷰**


```java
public class HashMap<K, V> extends AbstractMap<K, V>
        implements Map<K, V>, Cloneable, Serializable {

    final class EntrySet extends AbstractSet<Map.Entry<K, V>> {
        // size(), clear(), contains(), remove(), ...
    }

    final class KeySet extends AbstractSet<K> {
        // size(), clear(), contains(), remove(), ...
    }

    final class Values extends AbstractCollection<V> {
        // size(), clear(), contains(), remove(), ...
    }
}
```

`keySet()`, `entrySet()`, `values()`가 반환하는 자신의 컬렉션 뷰를 구현할 때 활용

## 익명 클래스

- 이름이 없는 클래스
- 바깥 클래스의 멤버가 아님
- 쓰이는 시점에 선언 + 인스턴스 생성

```java
public class Main {
		// 쓰이는 시점에 인스턴스 생성
    Inner innerClass(){
        return new Inner() {
            @Override
            public void hello(final String s) {
                System.out.println("hello");
            }
        };
    }
    
    interface Inner{
        void hello(String s);
    }
}
```

### 익명 클래스의 제약 사항

- 비정적인 문맥에서 사용될 때만 바깥 클래스의 인스턴스 참조 가능
    
    → static으로 선언된 메소드에서는 static만 접근이 가능하다.
    
- 정적 문맥에서 static final 상수 외의 정적 멤버를 가질 수 없다.
- **선언 지점에서만 인스턴스 생성 가능**
- instanceof 검사 및 클래스 이름이 필요한 작업 수행 불가
    
    → 선언과 동시에 인스턴스 생성하고 더이상 사용하지 않는다. 클래스 이름이 없다.
    
- 인터페이스 구현 및 다른 클래스의 상속 X
- 짧지 않으면 가독성 ↓

### 익명 클래스의 활용

- **즉석에서 작은 함수 객체나 처리 객체**를 만드는 데 주로 사용한다.
    
    ```java
    List<Integer> list = Arrays.asList(10, 5, 6, 7, 1, 3, 4);
    
    // 익명 클래스 사용
    Collections.sort(list, new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return Integer.compare(o1, o2);
        }
    });
    
    // 람다 도입 후
    Collections.sort(list, Comparator.comparingInt(o -> o));
    ```
    
- 정적 팩터리 메서드를 구현할 때도 사용한다.
    
    ```java
    static List<Integer> intArrayAsList(int[] a) {
        Objects.requiredNonNull(a);
        
        return new AbstracktList<>() {
            @Override public Integer get(int i) {
                return a[i];
            }
        }
    }
    ```
    

## 지역 클래스

- 지역 변수를 선언할 수 있는 곳이면 어디서든 선언 가능
- 유효 범위는 지역 변수와 동일

### 각 클래스와의 공통점

- `멤버 클래스`: 이름이 있고 반복해서 사용 가능
- `익명 클래스`
    - 비정적 문맥에서 사용될 때만 바깥 인스턴스 참조 가능
    - 정적 멤버를 가질 수 없음
    - 가독성을 위해 짧게 작성해야 함

```java
public class TestClass {
    private int number = 10;

    public TestClass() {
    }

    public void foo() {
        // 지역변수처럼 선언
        class LocalClass {
            private String name;
            // private static int staticNumber; // 정적 멤버 가질 수 없음

            public LocalClass(String name) {
                this.name = name;
            }

            public void print() {
                // 비정적 문맥에선 바깥 인스턴스를 참조 가능
                // foo()가 static이면 number에서 컴파일 에러
                System.out.println(number + name);
            }
        }
        LocalClass localClass1 = new LocalClass("local1"); // 이름이 있고
        LocalClass localClass2 = new LocalClass("local2"); // 반복해서 사용 가능
    }
}
```

## 핵심 정리

중첩 클래스에는 네 가지가 있으며, 각각의 쓰임이 다르다. 

메서드 밖에서도 사용해야 하거나 메서드 안에 정의하기엔 너무 길다면 멤버 클래스로 만든다. 

멤버 클래스의 인스턴스 각각이 바깥 인스턴스를 참조한다면 비정적으로, 그렇지 않다면 정적으로 만들자. 

중첩 클래스가 한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하는 지점이 단 한 곳이고 해당 타입으로 쓰기에 적합한 인터페이스나 클래스가 이미 있다면 익명 클래스로 만들고, 그렇지 않으면 지역 클래스로 만들자.