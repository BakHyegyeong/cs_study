# 아이템 78 : 공유 중인 가변 데이터는 동기화해 사용하라

# 동기화

- 배타적 실행 : 한 스레드가 변경 중인 객체를 다른 스레드가 보지 못하게 막는다.
    
    → 객체의 일관된 상태를 보장한다.
    
- 스레드 간 통신 : 수정 전의 최종 결과를 다른 스레드에게 보여준다.

## 동기화가 필요한 이유

long과 double 외의 변수는 원자적이다.

→ 여러 스레드가 동시에 변수의 값을 수정하고 있을 때 **정상적으로 수정되어 저장된 값을 읽어오는 것**을 보장한다. <br>
→ 하지만 한 스레드가 저장한 값이 **다른 스레드에게 보이는 것은 보장하지 않는다.** <br>
→ 즉 **볼 수 있다면 수정된 값을 보여주지만 볼 수 있는지 자체가 미지수**이다.

### 예시

```java
public class StopThread {

    private static boolean stopRequested;

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested) {
                i++;
            }
        });
        backgroundThread.start();
        
        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;
    }
}
```

- 백그라운드 스레드 : stopRequested의 값이 false라면 i의 값을 더하고 false라면 수행을 멈춘다.
- 메인 스레드 : 1초 후에 stopRequested의 값을 true로 바꾼다.

정상적으로 수행된다면 1초 후 백그라운드 스레드는 반복문을 빠져나와야 한다. <br>
하지만 위의 코드는 **무한루프에 빠지게 된다.**

→ **동기화를 하지 않아 메인 스레드가 수정한 값을 백그라운드 스레드가 보지 못하기 때문**이다.


> ⭐ 다른 스레드의 작업을 멈추는 Thread.stop 메서드는 안전하지 않아 사용하면 안된다.


### 해결방법 - synchronized

synchronized 키워드는 해당 메서드나 블록을 한번에 한 스레드씩 수행하도록 보장한다.

→ 이 코드에서는 동기화의 기능 중 스레드 간 통신 목적으로 사용되었다. 

```java
public class StopThread {
	private static boolean stopRequested;

	// 동기화
	private static synchronized void requestStop() {
		stopRequested = true;
	}
	
	// 동기화
	private static synchronized boolean stopRequested() {
		return stopRequested;
	}

	public static void main(String[] args) throws InterruptedException {
		Thread backgroundThread = new Thread(() -> {
			int i = 0;
			while (!stopRequested()) 
				i ++;
		});
		backgroundThread.start();

		TimeUnit.SECONDS.sleep(1);
		requestStop();
	}
}
```

**이때 읽기 메서드(`stopRequested`)와 쓰기 메서드(`requestStop`) 모두 동기화를 해야 한다.**

### 해결방법 - volatile

volatile 키워드는 synchronized보다 속도가 빠르면서 가장 최근에 기록한 값을 보여준다.

```java
public class StopThread {
	private static volatile boolean stopRequested;

	public static void main(String[] args) throws InterruptedException {
		Thread backgroudThread = new Tread(() -> {
			int i = 0;
			while (!stopRequested) 
				i ++;
		});
		backgroundThread.start();

		TimeUnit.SECONDS.sleep(1);
		stopRequested = true;
	}
}
```

## volatile 주의사항

복합 연산의 경우에는 volatile이 문제를 일으킬 수 있다.

```java
private static volatile int nextSerialNumber = 0;

public static int generateSerialNumber() {
	return nextSerialNumber++;
}
```

`nextSerialNumber`는 원자적으로 동작하지만 **`++`연산자로 인해 올바르게 동작하지 않는다.**

- `nextSerialNumber`에 접근
- `nextSerialNumber`의 값을 읽고 새로운 값을 저장

`++`연산자는 위와 같은 단계를 거치기 때문에 **이 단계 사이에 다른 스레드가 접근**한다면 다른 결과가 반환될 수 있다.

### 해결방법

1. synchronized 키워드를 메서드에 사용한다.
    
    → 이때 volatile를 제거해야 한다.
    
    ```java
    private static int nextSerialNumber = 0;
    
    public static int synchronized generateSerialNumber() {
    	return nextSerialNumber++;
    }
    ```
    

1. AtomicLong 타입을 사용한다.
    
    → volatile와 다르게 **스레드간 통신 뿐만 아니라 배타적 실행까지 지원**한다.
    
    ```java
    private static final AtomicLong nextSerialNumber = 0;
    
    public static int generateSerialNumber() {
    	return nextSerialNumber++;
    }
    ```
    

## 핵심정리

- 가변 데이터는 단일 스레드에서만 사용한다. 이외에는 불변 데이터만 공유하거나 아무것도 공유하지 말아야 한다.
- 여러 스레드가 가변 데이터를 공유한다면 그 데이터를 읽고 쓰는 동작은 반드시 동기화해야 한다. **동기화하지 않으면 한 스레드가 수행한 변경을 다른 스레드가 보지 못할 수도 있다.**
- 배타적 실행은 필요 없고 스레드 끼리의 통신만 필요하다면 `volatile` 한정자만으로 동기화할 수 있지만 사용하기 어렵다.