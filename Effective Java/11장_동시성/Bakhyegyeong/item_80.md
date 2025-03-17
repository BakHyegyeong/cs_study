# 아이템 80 : 스레드보다는 실행자, 태스크, 스트림을 애용하라

# 실행자 서비스

`java.util.concurrent` 패키지는 실행자 프레임워크(Executor Framework)라고 하는 **인터페이스 기반의 유연한 태스크 실행 기능**을 담고 있다. <br>
→ 원래는 아이템 49에서의 작업 큐를 사용해야 했다.

```java
// 작업 큐를 생성
ExecutorService exec = Executors.newSingleThreadExecutor();

// 실행자에 실행할 태스크를 넘긴다.
exec.execute(runnable);

// 실행자를 종료시킨다. 
// 이 작업이 실패하면 VM 자체가 종료되지 않는다.
exec.shutdown();
```

## 실행자 서비스의 주요 기능

- 특정 태스크가 완료되기를 기다린다.
- 태스크 모음 중 하나(`invokeAny` 메서드) 혹은 모든 태스크(`invokeAll` 메서드)가 완료되기를 기다린다.
- 실행자 서비스가 종료하기를 기다린다.
    
    → `awaitTermination` 메서드
    
- 완료된 태스크들의 결과를 차례로 받는다.
    
    → ExeutorCompletionService 이용
    
- 태스크를 특정 시간에 혹은 주기적으로 실행하게 한다.
    
    → ScheduledThreadPoolExecutor 이용
    

### 그 외의 기능

- 큐를 둘 이상의 스레드가 처리 : 다른 정적 팩터리를 이용하여 다른 종류의 실행자 서비스(스레드 풀)를 생성한다.
- ThreadPoolExecutor : 스레드 풀 동작을 결정하는 거의 모든 속성을 설정할 수 있다.
- Executors.newCachedThreadPool : 따로 설정할 필요가 없고 일반적인 용도에 적합하게 동작한다.
    
    → 주로 작은 프로그램이나 가벼운 서버에서 사용한다.
    
- Executors.newFixedThreadPool : 스레드 개수를 고정한다.
- ThreadPoolExecutor : 스레드를 완전히 통제한다.
    
    → 위 둘은 무거운 프로그램에서 주로 사용한다.
    

## 실행자 프레임워크 주의사항

- 작업 큐를 만드는 일과 스레드를 직접 다루는 것을 피해야 한다.
    
    → 실행자 프레임워크에서는 작업 단위와 실행 메커니즘이 분리되어 있다.
    
    - 태스크 : 작업 단위 (ex. Runnable : 반환값 X, Callable : 반환값 O)
    - 실행자 서비스 : 태스크를 수행하는 메커니즘

- 포크-조인 태스크를 지원해 CPU를 최대한 활용하면서 높은 처리량과 낮은 지연시간을 제공한다.
    - 포크-조인 태스크 : 작은 하위 태스크로 나뉠 수 있다.
    - 포크-조인 풀 : 이 안의 스레드들이 포크-조인 태스크들을 처리
        
        → 작업이 완료되면 다른 스레드의 남은 태스크를 가져와 대신 처리할 수 있다.