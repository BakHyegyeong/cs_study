# 아이템 39 : 명명 패턴보다 애너테이션을 사용하라

# 명명 패턴

도구나 프레임워크가 특별히 다뤄야 할 프로그램 요소에 사용되는 네이밍 룰

ex) JUnit에서 테스트 메서드 이름은 test로 시작해야 한다.

## 명명 패턴 단점

1. 오타에 민감하다.
    
    test로 시작해야하는데 실수로 이름을 tsetSafetyOverride로 지으면 테스트 메서드로 인식하지 못하고 테스트를 수행하지 않는다.
    
2. 올바른 프로그램 요소에서만 사용되리라 보증할 방법이 없다.
    
    메서드가 아닌 클래스 이름을 TestSafety로 지어 JUnit에게 줘도, 이 클래스 내의 테스트 메서드들의 테스트는 수행되지 않으며 Junit은 경고 메시지조차 출력하지 않는다.
    
3. 프로그램 요소를 매개변수로 전달할 마땅할 방법이 없다.
    
    특정 예외를 던져야 성공하는 테스트가 있을 때, 메서드 이름에 포함된 문자열로 예외를 알려주는 방법이 있지만 보기 흉할 뿐 아니라 컴파일러가 문자열이 예외 이름인지 알 도리가 없다.
    

→ 이런 명명 패턴의 단점을 애너테이션으로 해결할 수 있다.

## 마커 애너테이션 타입

아무 매개변수 없이 단순히 대상에 마킹하는 타입

- Test라는 이름의 애너테이션 선언
    
    → 자동으로 수행되는 간단한 테스트용 애너테이션으로 예외가 발생하면 테스트를 실패로 처리한다.
    

```java
import java.lang.annotation.*;

/**
 * 테스트 메서드임을 선언하는 애너테이션이다.
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
// @Test 애너테이션 선언
public @interface Test {
}
```

`@Test` 에너테이션 타입 선언 자체에 두 가지의 다른 애너테이션이 달려 있는데, 이를 **메타 애너테이션(meta-annotation)**이라 한다.

- @Retention(RetentionPolicy.RUNTIME) : @Test가 런타임에도 유지되어야 한다는 의미의 애너테이션
    
    → 생략하면 테스트 도구는 @Test를 인식할 수 없다.
    
- @Target(ElementType.METHOD) : `@Test`가 반드시 메서드 선언에만 사용되어야 한다고 알려주는 애너테이션
    
    → 클래스 선언, 필드 선언 등 다른 프로그램 요소에는 @Test 애너테이션을 달 수 없다.
    

### 사용 예시

- 마커 애너테이션을 사용한 프로그램 예시

```java
public class Sample {
    @Test
    public static void m1() { }        // 성공해야 한다.
    public static void m2() { }
    @Test 
    public static void m3() {    // 실패해야 한다.
        throw new RuntimeException("실패");
    }
    public static void m4() { }  // 테스트가 아니다.
    @Test 
    public void m5() { }   // 잘못 사용한 예: 정적 메서드가 아니다.
    public static void m6() { }
    @Test 
    public static void m7() {    // 실패해야 한다.
        throw new RuntimeException("실패");
    }
    public static void m8() { }
}
```

Sample 클래스에는 정적 메서드가 7개고, 그중 4개에 @Test를 달았다. 

- m3와 m7 메서드 : 예외를 던진다.
- m1 : 성공한다.
- m5 : 인스턴스 메서드이므로 @Test를 잘못 사용한 경우다.

→ 4개의 테스트 메서드 중 1개는 성공, 2개는 실패, 1개는 잘못 사용했다. 

→ @Test를 붙이지 않은 나머지 4개의 메서드는 테스트 도구가 무시할 것이다. 

<br>

`@Test` 애너테이션은 **Sample 클래스의 의미에 직접적인 영향을 주지 않고**, 단지 관심 있는 프로그램에게 추가 정보를 제공한다. 

→ 대상 코드의 의미는 그대로 둔 채 그 애너테이션에 관심 있는 도구에서 특별한 처리를 할 기회를 주는 것이다.

- 마커 애너테이션을 처리하는 프로그램(테스트 도구) 예시

```java
import java.lang.reflect.*;

public class RunTests {
    public static void main(String[] args) throws Exception {
        int tests = 0;  // 실행된 테스트의 총 수
        int passed = 0; // 성공적으로 실행된 테스트 수
        
        // 프로그램 실행 시 커맨드 라인에서 전달된 첫 번째 인자
        // 즉, 사용자로부터 클래스 이름을 입력받는다.
        Class<?> testClass = Class.forName(args[0]);
        
        // testClass의 모든 메서드를 가져온다.
        for (Method m : testClass.getDeclaredMethods()) {
		        
		        // 각 메서드가 @Test 애너테이션을 가지고 있는지 확인
            if (m.isAnnotationPresent(Test.class)) {
                tests++;  // tests 값 증가
                try {
                    m.invoke(null);  // 메서드 실행, static 메서드이기에 null을 전달
                    passed++;        // 성공적으로 수행됐다면 passed 값 증가
                } catch (InvocationTargetException wrappedExc) {
		                // 예외 처리
                    Throwable exc = wrappedExc.getCause();
                    System.out.println(m + " 실패: " + exc);
                } catch (Exception exc) {
                    System.out.println("잘못 사용한 @Test: " + m);
                }
            }
        }
        // 성공한 테스트 수와 실패한 테스트 수 반환
        System.out.printf("성공: %d, 실패: %d%n",
                passed, tests - passed);
    }
}
```

이 테스트 러너는 명령줄로부터 완전 정규화된 클래스 이름을 받아, 그 클래스에서 `@Test` 애너테이션이 달린 메서드를 찾아 차례로 호출한다. 그리고 애너테이션을 잘못 사용해 예외가 발생한다면 오류 메세지를 출력한다.

- `Class<?> testClass = Class.forName(args[0]);` : 사용자로부터 클래스 이름을 입력받는다.
- `for (Method m : testClass.getDeclaredMethods())` : testClass의 모든 메서드를 가져온다.
- `if (m.isAnnotationPresent(Test.class))` : 각 메서드가 @Test 애너테이션을 가지고 있는지 확인

## 매개변수를 받는 애너테이션 타입

- ExceptionTest 애너테이션

```java
import java.lang.annotation.*;

/**
 * 명시한 예외를 던져야만 성공하는 테스트 메서드용 애너테이션
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    Class<? extends Throwable> value();  // 매개변수이자 받고자 하는 예외 타입
}
```

이 애너테이션의 매개변수 타입은 `Class<? extends Throwable>` 이며(한정적 타입 토큰), 이는 "*Throwable을 확장한 클래스의 Class 객체* "라는 뜻이다. 

→ 모든 예외와 오류 타입을 수용한다.

### 사용 예시

- 매개변수 하나짜리 애너테이션을 사용한 프로그램 예시

```java
public class Sample2 {
	// 사용자가 받고자 하는 예외를 매개변수로 받는다.
	// 여기서는 ArithmeticException 예외를 받고자 한다.
    @ExceptionTest(ArithmeticException.class)
    public static void m1() {  // 성공해야 한다.
        int i = 0;
        i = i / i;
    }
    @ExceptionTest(ArithmeticException.class)
    public static void m2() {  // 실패해야 한다. (다른 예외 발생)
        int[] a = new int[0];
        int i = a[1];
    }
    @ExceptionTest(ArithmeticException.class)
    public static void m3() { }  // 실패해야 한다. (예외가 발생하지 않음)
}
```

`@ExceptionTest(ArithmeticException.class)` : 사용자가 받고자 하는 예외를 매개변수로 입력받는다.

- 마커 애너테이션과 매개변수 하나짜리 애너태이션을 처리하는 프로그램

```java
if (m.isAnnotationPresent(ExceptionTest.class)) {
        tests++;
        try {
               m.invoke(null);
               System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
        } catch (InvocationTargetException wrappedEx) {
			    // 실제 발생한 예외를 exc에 저장
                Throwable exc = wrappedEx.getCause();
               
               // getAnnotation 메서드를 통해 @ExceptionTest 애너테이션의 인스턴스를 가져온다.
               // value 메서드를 통해 애너테이션에서 받고자 하는 예외를 반환받는다.
               Class<? extends Throwable> excType =
               m.getAnnotation(ExceptionTest.class).value();
                
                // 발생한 예외가 받고자 하는 예외 타입과 일치하는지 확인
                if (excType.isInstance(exc)) {
                     passed++;
                } else {
                     System.out.printf(
                                "테스트 %s 실패: 기대한 예외 %s, 발생한 예외 %s%n",
                                m, excType.getName(), exc);
                }
           } catch (Exception exc) {
                System.out.println("잘못 사용한 @ExceptionTest: " + m);
           }
}
```

애너테이션 매개변수의 값을 추출하여 테스트 메서드가 올바른 예외를 던지는지 확인하는데 사용한다.

- `Class<? extends Throwable> excType =
               m.getAnnotation(ExceptionTest.class).value();`  : 사용자가 애너테이션의 매개변수로 입력한 예외를 반환한다.
    - `getAnnotation(ExceptionTest.class)`  : @ExceptionTest 애너테이션의 인스턴스를 반환
    - `.value()`  : 애너테이션의 매개변수로 입력한 예외를 반환
- `if (excType.isInstance(exc))` : 발생한 예외가 받고자 하는 예외 타입과 일치하는지 확인

## 다수의 예외를 명시하는 애너테이션

예외를 여러개 명시하고 그 중 하나가 발생하면 성공하게 할 수 있다.

## 배열 매개변수

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    Class<? extends Exception>[] value();   // Class 객체의 배열
}
```

매개변수 타입을 Class객체의 배열로 수정하였다.

### 사용 예시

- 배열 매개변수를 받은 애너테이션을 사용하는 코드
    
    ```java
    @ExceptionTest({ IndexOutOfBoundsException.class,
                         NullPointerException.class })
    public static void doublyBad() {   // 성공해야 한다.
         List<String> list = new ArrayList<>();
    
         // 자바 API 명세에 따르면 다음 메서드는 IndexOutOfBoundsException이나
         // NullPointerException을 던질 수 있다.
         list.addAll(5, null);
    }
    ```
    
    매개변수로 여러 예외를 입력하였다.
    

- 배열 매개변수를 받는 애너테이션을 처리하는 코드
    
    ```java
    if (m.isAnnotationPresent(ExceptionTest.class)) {
            tests++;
            try {
                 m.invoke(null);
                 System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
             } catch (Throwable wrappedExc) {
                 Throwable exc = wrappedExc.getCause();
                 int oldPassed = passed;
                 
                 // 반환값은 배열이다.
                 Class<? extends Throwable>[] excTypes =
                         m.getAnnotation(ExceptionTest.class).value();
                 for (Class<? extends Throwable> excType : excTypes) {
                     if (excType.isInstance(exc)) {
                           passed++;
                           break;
                     }
                  }
                  if (passed == oldPassed)
                      System.out.printf("테스트 %s 실패: %s %n", m, exc);
                  }
              }
     }
    ```
    

## @Repeatable

배열 매개변수 대신 `@Repeatable` **메타 애너테이션을 다는 방식을 선택하여 코드 가독성을 높일** 수 있다.

- 반복 가능한 ExceptionTest 애너테이션

    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @Repeatable(ExceptionTestContainer.class)  //컨테이너 애너테이션 class 객체
    public @interface ExceptionTest {
        Class<? extends Throwable> value();
    }
    ```

- 반복 가능한 ExceptionTest 애너테이션의 컨테이너 애너테이션

    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    public @interface ExceptionTestContainer {
        ExceptionTest[] value();     // value 메서드 정의
    }
    ```

### 주의 사항

1. `@Repeatable`을 단 ExceptionTest **애너테이션을 반환하는 '컨테이너 애너테이션'을 하나 더 정의**하고, `@Repeatable`에 이 컨테이너 애너테이션의 class 객체를 매개변수로 전달해야 한다.
2. 컨테이너 애너테이션은 내부 **애너테이션 타입의 배열을 반환하는 value 메서드를 정의**해야 한다.
3. 컨테이너 애너테이션 타입에는 적절한 보존 정책(@Retention)과 적용 대상(@Target)을 명시해야 한다.
    
    → 그렇지 않으면 컴파일 되지 않는다.
    

### 사용 예시

- 반복 가능한 애너테이션을 두 번 단 코드

    ```java
    @ExceptionTest(IndexOutOfBoundsException.class)
    @ExceptionTest(NullPointerException.class)
    public static void doublyBad() {
        List<String> list = new ArrayList<>();

        // 자바 API 명세에 따르면 다음 메서드는 IndexOutOfBoundsException이나
        // NullPointerException을 던질 수 있다.
        list.addAll(5, null);
    }
    ```

- 반복 가능 애너테이션을 처리하는 코드

    ```java
    if (m.isAnnotationPresent(ExceptionTest.class)
                        || m.isAnnotationPresent(ExceptionTestContainer.class)) {
            tests++;
            try {
                m.invoke(null);
                System.out.printf("테스트 %s 실패: 예외를 던지지 않음%n", m);
            } catch (Throwable wrappedExc) {
                Throwable exc = wrappedExc.getCause();
                int oldPassed = passed;
                ExceptionTest[] excTests =
                        m.getAnnotationsByType(ExceptionTest.class);
                for (ExceptionTest excTest : excTests) {
                    if (excTest.value().isInstance(exc)) {
                        passed++;
                        break;
                    }
                }
                if (passed == oldPassed)
                    System.out.printf("테스트 %s 실패: %s %n", m, exc);
                }
        }
    }
    ```

반복 가능 애터네이션은, 처리할 때도 주의 사항이 존재한다.

- 반복 가능 애너테이션을 여러개 달았으므로 하나만 달았을 때와 구분하기 위해 `m.isAnnotationPresent()` 에서 둘을 명확히 구분한다.
    
    → 만약 if문에서  `m.isAnnotationPresent(ExceptionTest.class` 만 조건으로 한다면 이는 반복 가능한 애너테이션과 일반 애너테이션을 구분할 수 없다.
    
    → 그렇기에 `m.isAnnotationPresent(ExceptionTestContainer.class)` 를 통해 컨테이너 애너테이션이 있는지 확인해 이 애너테이션이 반복 가능한 애너테이션인지 확인한다.
    
    → 반복 가능한 애너테이션만 저 조건에서 참을 얻을 수 있다.
    
- if 문에서 `m.isAnnotationPresent(ExceptionTestContainer.class)` 만 검사한다면 메서드에 직접 붙어 있는 `@ExceptionTest` 애너테이션을 무시하게 된다.
    
    → 테스트가 누락될 수 있다.
    

## 핵심 정리

애너테이션으로 할 수 있는 일을 명명 패턴으로 처리할 이유는 없으며, 자바 프로그래머라면 예외 없이 자바가 제공하는 애너테이션 타입들은 사용해야 한다.