# 자바의 2가지 객체 소멸자

자바의 객체 소멸자는 객체가 메모리에서 제거되기 전에 **객체에서 사용하는 리소스를 해제**하는 역할을 한다.

 

- finalizer : 예측할 수 없고 상황에 따라 위험할 수 있어 일반적으로 불필요하다.
- ceaner : finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고 느리며 일반적으로 불필요하다.

일반적으로 finalizer과 cleaner는 안전망 역할로 자원을 반납할 때나 네이티브 자원을 정리할 때 사용된다.

## finalizer과 cleaner의 문제점

### 1. 즉시 수행된다는 보장이 없다.

: 원하는 시점에 실행하게 하는 작업은 절대 수행할 수 없다.

### 2. 얼마나 빠르게 실행될지 알 수 없다.

: 가비지 컬렉터 알고리즘에 의해 가비지 컬렉터마다 다르므로 **프로그래머의 JVM과 고객의 JVM에서의 테스트 결과가 다를 수도 있다.**

### 3. 우선순위가 낮다.

: Finalizer 스레드는 우선순위가 낮아 실행될 기회를 얻지 못할 수도 있다.

→ 스레드가 대기하다가 스레드 안의 인스턴스가 GC되지 않고 쌓여 OutOfMemoryError를 발생시킬 수도 있다.

### 4. 아예 실행되지 않을 수도 있다.

: 수행여부조차 보장하지 않아 상태를 영구적으로 수정하는 작업에서는 절대 사용해서는 안된다.

→ System.gc나 System.runFinalization에 의해 실행될 가능성을 높여줄 수는 있지만 이 역시 보장하는 것은 아니다.

### 5. 예외를 처리하지 않는다.

: finalizer동작 중 발생한 예외는 무시되며, 처리할 작업이 남아있더라도 종료된다.

이 때문에 객체가 마무리가 덜 된 상태로 남을 수 있고 예외에 관한 경고조차 출력하지 않기 때문에 예외를 추적할 수도 없다.

### 6. 성능 문제

: try-with-resources로 자원을 닫는 경우 12ns가 소모되지만 finalizer의 경우 550ns가 소모되며 이는 가비지 컬렉터의 효율을 떨어지게 한다.

## ffinalizer과 cleaner를 사용하는 방법

: AutoCloseable를 구현하고 클라이언트에서 인스턴스를 다 사용하고 나면 close메서드를 호출하게 한다.

```java
public class Sample implements AutoCloseable {
    @Override
    public void close() {
        System.out.println("close");
    }
}
```

## finalizer과 cleaner를 사용할 때

### 1. 자원의 소유자가 close메서드를 호출하지 않는 것에 대비한 안전망 역할

cleaner, finalizer가 즉시 호출될것이란 보장은 없지만, 클라이언트가 하지 않은 자원 회수를 늦게라도 해주는 것이 아예 안하는 것보다 낫다.

자바에서는 안전망 역할의 finalizer로

FileInputSystem, FileOutputSystem, ThreadPoolExecutor가 있다.

```java
public class FileInputStream extends InputStream {
    //...
    protected void finalize() throws IOException {
        if ((fd != null) &&  (fd != FileDescriptor.in)) {
            close();
        }
    }
    //...
}
```

### 2. 네이티브 자원을 정리하는 경우

- 네이티브 피어 : 일반 자바 객체가 네티이브 메서드를 통해 기능을 위임한 네이티브 객체

### +) JNI와 네이티브 메서드

- JNI (Java Native Interface) : Java 프로그램이 다른 언어로 작성된 프로그램과 상호작용할 수 있게 해주는 인터페이스
    
    → C, C++ 처럼 인터프리터 없이 OS가 바로 읽을 수 있는 형태의 네이티브 코드를 JVM이 호출할 수 있게 하는 인터페이스 
    

![](# 자바의 2가지 객체 소멸자

자바의 객체 소멸자는 객체가 메모리에서 제거되기 전에 **객체에서 사용하는 리소스를 해제**하는 역할을 한다.

 

- finalizer : 예측할 수 없고 상황에 따라 위험할 수 있어 일반적으로 불필요하다.
- ceaner : finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고 느리며 일반적으로 불필요하다.

일반적으로 finalizer과 cleaner는 안전망 역할로 자원을 반납할 때나 네이티브 자원을 정리할 때 사용된다.

## finalizer과 cleaner의 문제점

### 1. 즉시 수행된다는 보장이 없다.

: 원하는 시점에 실행하게 하는 작업은 절대 수행할 수 없다.

### 2. 얼마나 빠르게 실행될지 알 수 없다.

: 가비지 컬렉터 알고리즘에 의해 가비지 컬렉터마다 다르므로 **프로그래머의 JVM과 고객의 JVM에서의 테스트 결과가 다를 수도 있다.**

### 3. 우선순위가 낮다.

: Finalizer 스레드는 우선순위가 낮아 실행될 기회를 얻지 못할 수도 있다.

→ 스레드가 대기하다가 스레드 안의 인스턴스가 GC되지 않고 쌓여 OutOfMemoryError를 발생시킬 수도 있다.

### 4. 아예 실행되지 않을 수도 있다.

: 수행여부조차 보장하지 않아 상태를 영구적으로 수정하는 작업에서는 절대 사용해서는 안된다.

→ System.gc나 System.runFinalization에 의해 실행될 가능성을 높여줄 수는 있지만 이 역시 보장하는 것은 아니다.

### 5. 예외를 처리하지 않는다.

: finalizer동작 중 발생한 예외는 무시되며, 처리할 작업이 남아있더라도 종료된다.

이 때문에 객체가 마무리가 덜 된 상태로 남을 수 있고 예외에 관한 경고조차 출력하지 않기 때문에 예외를 추적할 수도 없다.

### 6. 성능 문제

: try-with-resources로 자원을 닫는 경우 12ns가 소모되지만 finalizer의 경우 550ns가 소모되며 이는 가비지 컬렉터의 효율을 떨어지게 한다.

## ffinalizer과 cleaner를 사용하는 방법

: AutoCloseable를 구현하고 클라이언트에서 인스턴스를 다 사용하고 나면 close메서드를 호출하게 한다.

```java
public class Sample implements AutoCloseable {
    @Override
    public void close() {
        System.out.println("close");
    }
}
```

## finalizer과 cleaner를 사용할 때

### 1. 자원의 소유자가 close메서드를 호출하지 않는 것에 대비한 안전망 역할

cleaner, finalizer가 즉시 호출될것이란 보장은 없지만, 클라이언트가 하지 않은 자원 회수를 늦게라도 해주는 것이 아예 안하는 것보다 낫다.

자바에서는 안전망 역할의 finalizer로

FileInputSystem, FileOutputSystem, ThreadPoolExecutor가 있다.

```java
public class FileInputStream extends InputStream {
    //...
    protected void finalize() throws IOException {
        if ((fd != null) &&  (fd != FileDescriptor.in)) {
            close();
        }
    }
    //...
}
```

### 2. 네이티브 자원을 정리하는 경우

- 네이티브 피어 : 일반 자바 객체가 네티이브 메서드를 통해 기능을 위임한 네이티브 객체

### +) JNI와 네이티브 메서드

- JNI (Java Native Interface) : Java 프로그램이 다른 언어로 작성된 프로그램과 상호작용할 수 있게 해주는 인터페이스
    
    → C, C++ 처럼 인터프리터 없이 OS가 바로 읽을 수 있는 형태의 네이티브 코드를 JVM이 호출할 수 있게 하는 인터페이스 
    

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FVht26%2FbtsH1LekXMP%2FWRbyPlVG3RBKg4oowBeYx0%2Fimg.webp)

주로 아래와 같은 상황에서 유용하게 사용된다.

1. 표준 JAVA클래스 라이브러리가 플랫폼별 기능이나 프로그램 라이브러리를 지원하지 않는 경우
    
    → 애플리케이션을 JAVA 언어로 완전히 작성할 수 없는 상황
    
2. JAVA 애플리케이션에 액세스할 수 있도록 기존 애플리케이션(다른 프로그래밍 언어로 작성된)을 수정하는 경우

- Native Method : JAVA 언어 외부에서 구현된 메서드를 JAVA 코드에서 호출하는 메서드
    
    → native 키워드를 사용하여 선언되며 실제 구현은 JAVA가 아닌 C나 C++로 작성된다.
    
    → JVM 영역을 벗어나 운영체제의 기능이나 기존 네이티브 라이브러리에 접근할 수 있게 한다.
    
    ```java
    public class Thread implements Runnable { 
    ... 
    	public synchronized void start() { 
    		if (threadStatus != 0) 
    			throw new IllegalThreadStateException(); 
    			group.add(this); 
    			boolean started = false; 
    			
    			try { 
    				start0(); 
    				started = true; 
    			} finally {
    				 try { 
    					 if (!started) { 
    							 group.threadStartFailed(this); 
    					 } 
    				 } catch (Throwable ignore) { } 
    			} 
    } 
    private native void start0(); ... }
    ```)

주로 아래와 같은 상황에서 유용하게 사용된다.

1. 표준 JAVA클래스 라이브러리가 플랫폼별 기능이나 프로그램 라이브러리를 지원하지 않는 경우
    
    → 애플리케이션을 JAVA 언어로 완전히 작성할 수 없는 상황
    
2. JAVA 애플리케이션에 액세스할 수 있도록 기존 애플리케이션(다른 프로그래밍 언어로 작성된)을 수정하는 경우

- Native Method : JAVA 언어 외부에서 구현된 메서드를 JAVA 코드에서 호출하는 메서드
    
    → native 키워드를 사용하여 선언되며 실제 구현은 JAVA가 아닌 C나 C++로 작성된다.
    
    → JVM 영역을 벗어나 운영체제의 기능이나 기존 네이티브 라이브러리에 접근할 수 있게 한다.
    
    ```java
    public class Thread implements Runnable { 
    ... 
    	public synchronized void start() { 
    		if (threadStatus != 0) 
    			throw new IllegalThreadStateException(); 
    			group.add(this); 
    			boolean started = false; 
    			
    			try { 
    				start0(); 
    				started = true; 
    			} finally {
    				 try { 
    					 if (!started) { 
    							 group.threadStartFailed(this); 
    					 } 
    				 } catch (Throwable ignore) { } 
    			} 
    } 
    private native void start0(); ... }
    ```