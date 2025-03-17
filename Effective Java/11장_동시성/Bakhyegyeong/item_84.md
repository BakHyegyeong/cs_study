# 아이템 84 : 프로그램의 동작을 스레드 스케줄러에 기대지 말라

운영체제마다 구체적인 **스케줄링 정책이 다를 수 있기 때문에, 이 정책에 의존하면 안된다.**

→ 정확성이나 성능이 스레드 스케줄러에 따라 달라지는 프로그램이라면 다른 플랫폼에 이식하기 어렵다.

## 프로그램의 이식성을 높이는 방법

- 실행 가능한 스레드의 평균 수를 프로세서 수보다 적게 한다.
    
    → 전체 스레드 중 대기 중인 스레드와 실행 가능한 스레드를 구분해야 한다.
    
- 실행 준비가 된 스레드들은 맡은 작업을 완료할 때까지 계속 실행되도록 한다.

### **실행 가능한 스레드 수를 적게 유지하는 방법**

- 스레드가 작업을 완료한 후에는 다음 작업이 생길 때까지 대기하도록 한다.
- 스레드가 당장 처리해야할 작업이 없다면 실행되지 않도록 한다.
    
    ex) 실행자 프레임워크로 스레드 풀 크기를 적절히 설정하고 작업을 짧게 설정해 스레드에 분배한다.
    

## 스레드 주의사항

### 바쁜 대기 상태 회피하기

바쁜 대기 상태란 임계 영역(critical area)에서 작업 중인 스레드 B를 기다리는 스레드 A가 있을 때, **스레드 A가 임계 영역에 들어갈 수 있는 지 계속해서 검사하는 상태**를 의미한다.

→ 바쁜 대기는 스레드 스케줄러의 변덕에 취약하며, 프로세서에 큰 부담을 주어 다른 유용한 작업이 실행될 기회를 박탈한다.

```java
public class SlowCountDownLatch { 
  private int count;

  public SlowCountDownLatch(int count) {
    if (count < 0)
      throw new IllegalArgumentException(count + " < 0");
    this.count = count;
  }

  public void await() {
	// 조건을 충족하는지 계속해서 검사한다.
    while (true) {
      synchronized(this) {
        if (count == 0)
          return;
      }
    }
  }
  public synchronized void countDown() {
    if (count != 0)
      count--;
  }
}
```

### Thread.yield**에 의존하지 않기**

Thread.yield를 통해 스레드에 우선순위를 부여하는 것은 당장 성능은 높아질 수 있으나 이식성이 낮아진다. 

→ 이 방법보다 애플리케이션 구조를 바꿔 동시에 실행 가능한 스레드 수가 적어지도록 하는 것이 좋다.

- 처음에는 yield를 이용해 성능이 높아지더라도 두 번째부터는 아무 효과가 없을 수도, 심지어 성능 저하가 발생할 수도 있다.
    
    → 매번 같은 결과를 보장하지 않으며 OS에 따라 결과가 달라질 수 있다.
    
- Thread.yield는 테스트할 수단이 없다.


> ⭐ **Thread.yield**
> 
> 현재 실행 되고 있는 스레드가 다른 스레드를 위해서 양보하겠다는 **힌트를 주는 메서드**이다. <br>
> → **스케줄러가 힌트를 알아차린 경우에만** 양보가 되기 때문에 실행을 보장할 수는 없다.


**예시**

```java
class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++)
            System.out.println(Thread.currentThread().getName() + " in control");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        // 서브 스레드 실행
        t.start();
        
        for (int i = 0; i < 5; i++) {
            // 서브 스레드에 양도
            Thread.yield();
        
            // 후에 메인 스레드 실행
            System.out.println(Thread.currentThread().getName() + " in control");
        }
    }
}
```

서브 → 메인 → 서브 → 메인식으로 결과가 나올 것을 예상하지만 실제로는 **아래와 같이 섞여 나오고 결과도 매번 달라진다.**

```java
// 결과 1
main in control
Thread-0 in control
Thread-0 in control
Thread-0 in control
Thread-0 in control
Thread-0 in control
main in control
main in control
main in control
main in control

// 결과 2
main in control
main in control
Thread-0 in control
Thread-0 in control
Thread-0 in control
main in control
Thread-0 in control
Thread-0 in control
main in control
main in control
```

## 핵심 정리

- 프로그램의 동작을 OS에 따라 다르게 동작하는 스레드 스케줄러에 기대지말자. 견고성과 이식성을 모두 해치는 행위다.
- 같은 이유로, Thread.yield와 스레드 우선순위에 의존하지 말자. 이 기능들은 스레드 스케줄러에 제공하는 힌트일 뿐이다.