# 아이템 47 : 반환 타입은 스트림보다 컬렉션이 좋다.

# **일련의 원소를 반환하는 메서드**

- 스트림이 도입되기 이전 : Collection, Set, List과 같은 컬렉션 인터페이스나 Iterable 또는 배열을 사용했다.

- 스트림이 도입된 후
    - for-each문은 `Iterable` 인터페이스를 구현하고 있어야 사용이 가능하다.
    - `Stream` 인터페이스는 `Iterable` 인터페이스가 정의한 추상 메서드를 포함하고 정의한 방식대로 동작하지만, **확장(extend)** 하지 않았다.
    - 그렇기에 스트림은 for-each를 통한 반복을 지원하지 않는다.
    
    ```java
    // Stream.iterator는 컴파일 오류로 실행하지 못한다.
    for(ProcessHandle p: ProcessHandle.allProcesses.iterator()) {
    
    }
    ```
    

## 스트림을 반복할 수 있게 하는 방법

### 어댑터

- **Stream → Iterable**
    
    ```java
    public static <E> Iterable<E> iterableOf(Stream<E> stream) { 
            return stream::iterator;
    } 
    
    // 사용
    for(ProcessHandle p : iterableOf(ProcessHandle.allProcesses())){
    }
    ```
    
- **Iterable → Stream**
    
    ```java
    public static <E> Stream<E> streamOf(Iterable<E> iterable) {
           return StreamSupport.stream(iterable.spliterator(), false);
    }
    ```
    

### **Collection 인터페이스**

**반복(iterable)과 스트림을 동시에 지원한다.**

→  **공개 API를 작성할 때**는 스트림 파이프라인을 사용하는 사람과 반복문에서 쓰려는 사람 모두를 배려해 **`Collection`이나 그 하위 타입으로 반환**하도록 해야 한다.

```java
public interface Collection<E> extends Iterable<E> {
    ...
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
    
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
}
```

## 전용 컬렉션 구현

- 원소 사이즈가 작은 경우 : 표준 컬렉션 사용
- 원소 사이즈가 큰 경우 : 전용 컬렉션 구현
    
    → 반환할 시퀀스가 크지만 표현을 간결하게 할 수 있다.
    

### 예시 - 멱집합

> ⭐
>
> **{a, b, c}의 멱집합**
>
> 원소의 개수가 n개일 때 원소 개수는 **2^n**개가 된다.
>
> ```java
> 000 : {}
> 001 : {a}
> ...
> 111 : {a, b, c}
> ```


```java
// 입력 집합의 멱집합을 전용 컨렉션에 담아 반환
public class PowerSet {
    public static final <E> Collection<Set<E>> of(Set<E> s) {
        List<E> src = new ArrayList<>(s);
            if(src.size() > 30) {
                throw new IllegalArgumentException("집합에 원소가 너무 많습니다(최대 30개).: " + s);
            }
            
            return new AbstractList<Set<E>>() {
                @Override
                public int size() {
                    return 1 << src.size();
                }
                
                @Override
                public boolean contains(Object o) {
                    return o instanceof Set && src.containsAll((Set) o);
                }
                
                @Override
                public Set<E> get(int index) {
                    Set<E> result = new HashSet<>();
                        for (int i = 0; index != 0; i++, index >>=1) {
                            if((index & 1) == 1) { // index는 멱집합을 가지고 있는 Set<E>의 비트 연산 값
                            // src는 각 멱집합의 원소를 가진 객체
                            // index가 5이면 -> 101 이므로 a,c 를 가지고 있다.
                                result.add(src.get(i));
                            }
                        }
                        return result; // 만들어진 집합 return
                    }
                };
            }
        }
```

## 스트림 반환 예제

입력 리스트의 연속적인 부분 리스트를 모두 반환하는 메서드를 구현하고자 하는 상황이다.

- 이중 반복문 사용 : 입력 리스트 크기의 거듭제곱(`O(n^2)`)만큼 메모리를 차지하게 된다.
    
    ```java
    for (int start = 0; start < src.size(); start++) {
    	for (int end = start + 1; end <= src.size(); end++) {
        	System.out.println(src.subList(start, end));
        }
    }
    ```
    

- 스트림 사용 
    
    ```java
    public class SubList {
    
    		// prefixes : 1부터 list.size()까지 모두 index를 걸고 
        // index값이 end로 들어가 서브리스트를 만든다.
        // (a), (a,b), (a,b,c)
        public static <E> Stream<List<E>> prefixes(List<E> list) {
            return IntStream.rangeClosed(1, list.size())
                .mapToObj(end -> list.subList(0, end));
        }
        
        // suffixes : 0부터 list.size()까지 모두 index를 걸고
        // index값이 start로 들어가 서브 리스트를 만든다.
        // (a,b,c), (b,c), (c)
        public static <E> Stream<List<E>> suffixes(List<E> list) {
            return IntStream.rangeClosed(0, list.size())
                .mapToObj(start -> list.subList(start, list.size()));
        }
        
        // of : Stream.concat 을 통해서 반환되는 스트림에 빈 리스트를 추가
        // flatMap 을 통해 prefix로 만든 부분 리스트를 다시 suffix로 수행한다.
        // [{a},{a,b},{a,b,c}],[{a,b},{a,b,c}],[{a,b,c}]
        public static <E> Stream<List<E>> of(List<E> list) {
            return Stream.concat(Stream.of(Collections.emptyList()),
                prefixes(list).flatMap(SubList::suffixes));
        }
    
    }
    ```
    

## 핵심 정리

반환 전부터 이미 원소들을 컬렉션에 담아 관리하고 있거나 원소 개수가 적다면 `ArrayList` 과 같은 표준 컬렉션에 담아 반환하자. 

그렇지 않다면, 전용 컬렉션을 구현할 수도 있다. 

컬렉션을 반환하는게 불가능하다면 스트림과 `Iterable` 중 더 자연스러운 것을 반환하면 된다.

[코드 참조](https://tester-1.tistory.com/45)