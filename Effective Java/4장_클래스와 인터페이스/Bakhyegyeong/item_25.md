# 아이템 25 : 톱레벨 클래스는 한 파일에 하나만 담으라

# 톱 레벨 클래스

함수, 클래스 또는 다른 무언가로 감싸지지 않은 모든 구문은 톱-레벨로 간주된다.

→ 톱레벨 클래스란 중첩 클래스가 아닌 것

## 톱 레벨 클래스는 한 파일에 하나만 담아야 한다.

한 클래스를 여러 가지로 정의할 수 있으며, 그 중 어느 것을 사용할지는 **어느 소스를 먼저 컴파일하느냐에 따라 달라지기 때문이다.**

- Utensil.java
    
    ```java
    class Utensil {
        static final String NAME = "pan";
    }
    
    class Dessert {
        static final String NAME = "cake";
    }
    ```
    
- Main.java
    
    ```java
    public class Main {
    
        public static void main(String[] args) {
            System.out.println(Utensil.NAME + Dessert.NAME);
        }
    }
    ```
    
    이때 Main을 실행하게 되면 정상적으로 pancake를 출력하게 된다.
    
- Dessert.java
    
    ```java
    class Utensil {
        static final String NAME = "pot";
    }
    
    class Dessert {
        static final String NAME = "pie";
    }
    ```
    
    Dessert.java가 생성된 후에 **`javac Main.java Dessert.java` 명령을 수행하면 컴파일 에러가 발생**한다.
    

### 발생 원인

**컴파일러의 동작 과정**

1. 먼저 `Main.java` 컴파일
2. 그 안에서 `System.out.println(Utensil.NAME + Dessert.NAME);` 구문을 만나기 때문에 **`Utensil.java`를 컴파일해서 Utensil, Dessert 클래스를 찾아낸다.**
3. 2번째 인수로 넘어온 **`Dessert.java`을 컴파일 하려고 할 때 같은 클래스가 이미 정의되어 있는 것**을 알게된다.
    
    → 오류 발생
    

### 명령어별 수행 결과

- `javac Main.java` or `javac Main.java Utensil.java` : pancake 출력
- `javac Dessert.java Main.java` : potpie 출력

**컴파일러에 어느 소스파일을 먼저 건네느냐**에 따라서 **정상 동작할수도 또는 컴파일 오류가 발생**할 수도 있다. 

이런 문제가 발생하지 않게 바로잡아야한다.

## 해결 방법

**톱레벨 클래스들을 서로 다른 소스 파일로 분리**한다.

```java
// Utensil.java
public class Utensil {
    static final String NAME = "pan";
}
```

```java
// Dessert.java
public class Dessert {
    static final String NAME = "cake";
}
```

### 굳이 톱레벨 클래스를 한 파일에 담고 싶다면

**톱레벨 클래스들을 정적 멤버 클래스로 변경**한다.

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }

    private static class Utensil {
        static final String NAME = "pan";
    }

    private static class Dessert {
        static final String NAME = "cake";
    }
}
```

## 핵심 정리

소스 파일 하나에는 반드시 톱레벨 클래스(혹은 톱레벨 인터페이스)를 하나만 담자. 

이 규칙만 따른다면 컴파일러가 한 클래스에 대한 정의를 여러 개 만들어내는 일은 사라진다. 

소스 파일을 어떤 순서로 컴파일하든 바이너리 파일이나 프로그램의 동작이 달라지는 일은 결코 일어나지 않을 것이다.