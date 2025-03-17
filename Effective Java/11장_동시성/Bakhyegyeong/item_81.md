# 아이템 81 : wait와 notify 보다는 동시성 유틸리티를 애용하라

자바 5에서 도입된 고수준의 동시성 유틸리티가 wait와 notify로 하드코딩해야 했던 일들을 대신 처리해준다.

# 동시성 유틸리티

- 실행자 프레임워크
- 동시성 컬렉션(concurrent collection)
- 동기화 장치(synchronizer)

# 동시성 컬렉션

List, Queue, Map 같은 표준 컬렉션 인터페이스에 동시성을 가미해 구현한 고성능 컬렉션이다.

- 높은 동시성에 도달하기 위해 **동기화를 각자의 내부에서 수행**한다.
- **동시성 컬렉션에서 동시성을 무력화하는 건 불가능**하며, 외부에서 락을 추가로 사용하면 오히려 속도가 느려진다.
- 여러 메서드를 원자적으로 묶어 호출하는 것도 불가능하다.
    
    → 여러 기분 동작을 하나의 원자적 동작으로 묶는 **상태 의존적 수정 메서드들이 추가**되었다.
    

## 동시성 컬렉션 예시

### ConcurrentMap

ConcurrentMap은 동시성이 뛰어나며 속도가 빠르다.

```java
private static final ConcurrentMap<String, String> map = new ConcurrentHashMap<>();

public static String intern(String s) {
    String previousValue = map.putIfAbsent(s, s);
    return previousValue == null ? s : previousValue;
}

// 개선
public static String intern(String s) {
    String result = map.get(s);
    if (result == null) {
        result = map.putIfAbsent(s, s);
        if (result == null)
            result = s;
    }
    return result;
}
```

- `putIfAbsent(key, value)` 메서드는 map 안에 해당 키에 매핑된 값이 없을 때만 새 값을 넣는다.
    
    → 기존 값이 있었다면 그 값을 반환하고, 없었다면 null을 반환한다.
    
- ConcurrentHashMap은 get 같은 검색 기능에 최적화되었다.
    
    → get을 먼저 호출하여 필요할 때만 putIfAbsent를 호출하면 더 빠른 속도를 제공할 수 있다.
    

### **BlockingQueue**

컬렉션 인터페이스 중 일부는 **작업이 성공적으로 완료될 때까지 기다리도록 확장**되었다.

→ Queue를 확장한 BlockingQueue가 그 예시이다.

- 동작 과정
    - take 메서드를 통해 큐의 첫 원소를 꺼낸다.
    - 큐가 비어있다면 새로운 원소가 추가될 때까지 기다린다.
        
        → 이런 특성 덕에 **작업 큐(생산자-소비자 큐)로 쓰기 적합**하다.
        
- 작업 큐
    - 생산자 스레드 : 작업을 큐에 추가한다.
    - 소비자 스레드 : 큐에 있는 작업을 꺼내 처리한다.

```java
BlockingQueue<Integer> bq = new ArrayBlockingQueue<Integer>(10);
Thread consumer= new Thread(() -> {
    System.out.println(bq.take());
});
Thread producer= new Thread(() -> {
    bq.add(10);
});

consumer.start();
producer.start();
```

# 동기화 장치

스레드가 다른 스레드를 기다릴 수 있게 하여, 서로 작업을 조율할 수 있게 해준다.

→ 예시로 CountDownLatch와 Semaphore, **phaser**가 있다.

## 동기화 장치 예시 - **CountDownLatch**

일회성 장벽으로, 하나 이상의 스레드가 또 다른 **하나 이상의 스레드 작업이 끝날 때까지 기다리게 한다.**

→ CountDownLatch의 유일한 생성자는 int 값을 받으며, 이 값이 래치의 **countDown 메서드를 몇 번 호출해야 대기 중인 스레드들을 깨우는지를 결정**한다.

**예시**

- 타이머 스레드가 시계를 시작하기 전에 모든 작업자 스레드는 동작을 수행할 준비를 마친다.
- 마지막 작업자 스레드가 준비를 마치면 타이머 스레드가 시작 방아쇠를 당겨 작업자 스레드들이 일을 시작하게 한다.
- 마지막 작업자 스레드가 동작을 마치자마자 타이머 스레드는 시계를 멈춘다.

```java
public static long time(Executor executor, int concurrency, Runnable action) throws InterruptedException {
    CountDownLatch ready = new CountDownLatch(concurrency);
    CountDownLatch start = new CountDownLatch(1);
    CountDownLatch done  = new CountDownLatch(concurrency);

    for (int i = 0; i < concurrency; i++) {
        executor.execute(() -> {
			// 타이머에게 준비를 마쳤음을 알린다.
            ready.countDown(); 
            try {
				// 모든 작업자 스레드가 준비될 때까지 기다린다.
                start.await();
                action.run();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            } finally {
				// 타이머에게 작업을 마쳤음을 알린다.
                done.countDown();  
            }
        });
    }

    ready.await();     // 모든 작업자가 준비될 때까지 기다린다.
    long startNanos = System.nanoTime();
    start.countDown(); // 작업자들을 깨운다.
    done.await();      // 모든 작업자가 일을 끝마치기를 기다린다.
    return System.nanoTime() - startNanos;
}
```

# **wait와 notify를 사용해야 하는 때**

## wait 메서드

wait 메서드는 스레드가 어떤 조건이 충족되기를 기다리게 할 때 사용한다.

- 락 객체의 wait 메서드는 **반드시 그 객체를 잠근 동기화 영역 안에서 호출**해야 한다.
- wait 메서드를 사용할 때는 반드시 대기 반복문(wait loop) 관용구를 사용해야 한다.
- 조건이 충족되지 않았는데 스레드가 동작을 이어가면 락이 보호하는 불변식을 깨뜨릴 위험이 있다.

```java
synchronized (obj) {
    while (<조건이 충족되지 않았다>)
        obj.wait(); // (락을 놓고, 깨어나면 다시 잡는다.)
        
    ... // 조건이 충족됐을 때의 동작을 수행한다.
}
```

### 조건이 만족되지 않아도 스레드가 깨어날 수 있는 상황

- 스레드가 notify를 호출한 다음 대기 중이던 스레드가 깨어나는 사이에 다른 스레드가 락을 얻어 그 락이 보호하는 상태를 변경한다.
- 조건이 만족되지 않았음에도 다른 스레드가 실수로 혹은 악의적으로 notify를 호출한다.
    
    → 공개된 객체를 락으로 사용해 대기하는 클래스는 이런 위험에 노출된다.
    
    → 외부에 노출된 객체의 동기화된 메서드 안에서 호출하는 wait는 모두 이 문제에 영향을 받는다.
    
- 대기 중인 스레드 중 일부만 조건이 충족되어도 notifyAll을 호출해 모든 스레드를 깨울 수도 있다.
- 대기 중인 스레드가 드물게 notify 없이도 깨어나는 경우가 있다.
    
    → 허위 각성(spurious wakeup)
    

## **notify vs notifyAll**

- notify : 모든 스레드가 같은 조건을 기다리고, 조건이 한 번 충족될 때마다 단 하나의 스레드만 혜택을 받을 경우
- notifyAll : 위의 경우를 포함한 일반적인 상황
    - 깨어나야 하는 모든 스레드가 깨어나기에 항상 정확한 결과를 얻을 수 있다.
        
        → 다른 스레드까지 깨어날 수도 있지만 깨어난 스레드들은 조건이 충족되었는지 다시 확인하여, **충족되지 않았다면 다시 대기상태에 들어가게 된다.**
        
    - notify 대신 notifyAll을 사용하면 **관련 없는 스레드가 실수로 혹은 악의적으로 wait를 호출하는 공격으로부터 보호**할 수 있다.

# 핵심 정리

- 코드를 새로 작성한다면 wait와 notify를 쓸 이유가 거의 없다.
- 이들을 사용하는 레거시 코드를 유지보수해야 한다면 wait는 항상 표준 관용구에 따라 while 문 안에서 호출하도록 하자.
- 일반적으로 notify보다는 notifyAll을 사용해야 한다.
- 혹시라도 notify를 사용한다면 응답 불가 상태에 빠지지 않도록 각별히 주의하자.