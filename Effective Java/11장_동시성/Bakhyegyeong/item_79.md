# 아이템 79 : 과도한 동기화는 피하라

과도한 동기화는 오히려 성능을 떨어트리고, 교착상태에 빠뜨린다.

또한 동기화 메서드나 동기화 블록 안에서는 **제어를 절대로 클라이언트에 양도하면 안 된다.**

## 과도한 동기화 예시

아래의 코드는 집합에 원소가 추가되면 클라이언트에 알림을 전송하는 코드이다.

```java
public class ObservableSet<E> extends ForwardingSet<E> {

    public ObservableSet(Set<E> set) {
        super(set);
    }

	// 구독자를 관리하는 set
    private final List<SetObserver<E>> observers = new ArrayList<>();

	// 구독 신청
    public void addObserver(SetObserver<E> observer) {
        synchronized (observers) {
            observers.add(observer);
        }
    }
    
    // 구독 해제
    public boolean removeObserver(SetObserver<E> observer) {
        synchronized (observers) {
            return observers.remove(observer);
        }
    }
    
    private void notifyElementAdded(E element) {
        synchronized (observers) {
            for(SetObserver<E> observer : observers) {
                observer.added(this, element);
            }
        }
    }
    
    @Override
    public boolean add(E element) {
        boolean added = super.add(element);
        if(added) {
		    
            // 추가되면 알림
            notifyElementAdded(element); 
        }
        return added;
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        boolean result = false;
        for (E element : c) {
            result |= add(element); 
        }
        return result;
    }
}
```

이 코드는 외부에서 함수 객체( `SetObserver` )를 받아오기 때문에 아래와 같은 두가지 에러가 발생할 수 있는 위험에 노출되어 있다.

### **ConcurrentModificationException 예외**

위의 클래스를 활용해 0~23까지 출력한 후 자신을 구독해지하는 코드를 작성하였다.

```java
public static void main(String[] args) {
	ObservableSet<Integer> set = new ObservableSet<>(New HashSet<>());
	
	set.addObserver(new SetObserver<Integer>() {
		public void added(ObservableSet<Integer> s, Integer e) {
			System.out.println(e);
			if (e == 23) s.removeObserver(this);
		}
	});

	for (int i = 0; i < 100; i++) 
		set.add(i); // notifyElementAdded 메서드 호출
}
```

이 코드는 23을 출력한 후 **ConcurrentModificationException 예외가 발생한다.**

→ `added`메서드가 add메서드 안의 **`notifyElementAdded` 메서드가 호출될 때 실행**되기 때문이다.

- `added` 메서드 동작
    - added 메서드는 ObservableSet의 removeObserver메서드를 호출한다.
    - removeObserver 메서드는 remove 메서드를 호출한다.
    - remove 메서드는 리스트에서 원소를 제거하는 메서드로 제거를 위해 리스트를 순회하게 된다.
- `notifyElementAdded` 메서드 동작
    - observers 리스트를 순회하면서 각각의 observer 객체에 added 메서드를 수행한다.

→ 즉 **리스트를 순회하는 도중 리스트의 원소를 제거**하려고 하기에 오류가 발생한다.

→ `notifyElementAdded`메서드 안에서 observers 리스트를 `synchronized` 로 감싸고 있어도 **콜백을 거쳐 수정되는 것은 막지 못하기 때문**이다.

### 교착 상태

아래의 코드는 구독을 해지하는 관찰자를 작성하는데 `removeObserver`를 직접 호출하지 않고 실행자 서비스를 사용해 다른 스레드에게 부탁하는 코드이다.

```java
set.addObserver(new SetObserver<Integer>() {
    public void added(ObservableSet<Integer> s, Integer e) {
        System.out.println(e);
        if (e == 23) {
            ExecutorService exec = Executors.newSingleThreadExecutor();
            try {
                exec.submit(() -> s.removeObserver(this)).get();
            } catch (ExecutionException | InterruptedException ex) {
                throw new AssertionError(ex);
            } finally {
                exec.shutdown();
            }
        }
    }
});
```

**메인 스레드가 `addObserver` 에서 이미 observers 에 대한 락을 쥐고 있기 때문**에, 다른 백그라운드 스레드가 `removeObserver` 를 호출 시 락을 얻을 수 없어 교착 상태가 발생한다.

## 과도한 동기화 해결 방법

### 외계인 메서드 분리

외부 메서드 호출을 동기화 블록 바깥으로 옮긴다.

```java
private final List<SetObserver<E>> observers = new CopyOnWriteArrayList<>();

public void addObserver(SetObserver<E> observer) {
		observers.add(observer);
}

public boolean removeObserver(SetObserver<E> observer) {
		return observers.remove(observer);
}

private void notifyElementAdded(E element) {
		for (SetObserver<E> observer : observers)
				observer.added(this, element);
}
```

> ⭐ 외계인 메서드 : 동기화된 영역 안에서 재정의 메서드를 호출하거나 클라이언트가 넘겨준 함수 객체를 호출하는 것


### 복사본 사용

**관찰자 리스트를 복사해 쓰면** 락 없이도 안전하게 순회할 수 있다.

```java
private void notifyElementAdded(E element) {
    List<SetObserver<E>> snapshot = null;
    synchronized(observers) {
        snapshot = new ArrayList<>(observers);
    }
    for (SetObserver<E> observer : snapshot)
        observer.added(this, element);
}
```

## 동기화 시 주의점

과도하게 동기화를 하면, 병렬로 실행을 못하게 되기 때문에 경쟁하고 메모리를 일관되게 보기 위한 지연 시간으로 인해 오히려 비효율적이다.

1. 외부에서 동기화 : 해당 클래스를 동시에 사용해야 하는 클래스가 **외부에서 객체 전체에 동기화**를 거는 방식
2. 내부에서 동기화 : 락 분할(lock splitting), 락 스트라이핑(lock striping), 비차단 동시성 제어(nonblocking concurrecy control) 등 다양한 기법을 동원해 동시성을 높여줄 수 있다.

## 핵심 정리

교착상태와 데이터 훼손을 피하려면 동기화 영역 안에서 외계인 메서드를 호출하지 말고 작업을 최소한으로 줄여야 한다. 

멀티코어 세상에서는 과도한 동기화를 피하는게 좋기 때문에 합당한 이유가 있을 때만 내부에서 동기화를 시키고, 문서에 정확히 여부를 밝히도록 하자.