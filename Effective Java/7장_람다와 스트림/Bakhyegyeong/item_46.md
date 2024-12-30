# 아이템 46 : 스트림에는 부작용 없는 함수를 사용하라

# 스트림의 핵심 패러다임

계산을 **일련의 변환으로 재구성**하는 부분

→ 각 변환의 단계는 **이전 단계의 결과를 받아 처리하는 순수 함수**여야 한다.

**→ 스트림 연산에 건네는 함수 객체는 모두 부작용(side effect)이 없어야 한다.**

> ⭐
>
> **순수 함수**: 오직 입력만이 결과에 영향을 주는 함수  
> → 다른 가변 상태를 참조하지 않고, 함수 스스로도 다른 상태를 변경하지 않아야 함
>
> **부작용 (side effect)**: 자신의 스코프 밖의 변수 상태 등을 변경하는 메소드, 프로시저


## 부작용을 일으키는 스트림 예시

```java
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner(file).tokens()) {
	words.forEach(word -> {
		freq.merge(word.toLowerCase(), 1L, Long::sum);
	});
}
```

모든 작업이 종단 연산인 `forEach` 에서 일어나고 있는데 이때 **외부 상태(빈도표)를 수정**하는 람다를 실행하고 있다.

**올바른 활용**

```java
Map<String, Long> freq;
try (Stream<String> words = new Scanner(file).tokens()) {
     freq = words.collect(groupingBy(String::toLowerCase, counting()));
}
```

위와 같이 사용해야 올바르게 스트림을 사용한다고 할 수 있다.

> ⭐
>
> **ForEach 연산**은 종단 연산 중 기능이 가장 적고 병렬화할 수도 없기 때문에 **계산 결과를 보고할 때만 사용하고 계산하는 데는 쓰지 말아야 한다.**


# 병렬 처리와 부작용

## forEach와 병렬 처리

```java
@Test
void forEachSideEffectTest() {
    List<Integer> matched = new ArrayList<>();
    List<Integer> elements = new ArrayList<>();

    for (int i = 0; i < 100; i++) {
        elements.add(i);
    }

		// parallelStream()으로 병렬처리를 하고 있다.
    elements.parallelStream()
        .forEach(e -> {
            if (e >= 90) {
                System.out.println("ThreadId: " + Thread.currentThread().getId() + " Size: " + matched.size());
                matched.add(e);
            }
        });

    System.out.println(matched);
}

// 결과
ThreadId: 21 Size: 0
ThreadId: 21 Size: 1
ThreadId: 1 Size: 0
ThreadId: 1 Size: 3
ThreadId: 1 Size: 4
ThreadId: 22 Size: 0
ThreadId: 22 Size: 6
ThreadId: 22 Size: 7
ThreadId: 23 Size: 0
ThreadId: 23 Size: 9
[96, 97, 90, 91, 92, 93, 94, 95, 98, 99]
```

`forEach` 에서 외부 변수인 `matched` 의 상태를 바꾸려고 하고 있다.

→ **여러 스레드가 `matched`에 접근**하고 있고 **별다른 `lock` 이 걸려있지 않기 때문**에 **`matched size` 가 매 실행마다 다른 것**을 결과에서 볼 수 있다.

### 올바른 활용

```java
@Test
void forEachSideEffectTest() {
    List<Integer> elements = new ArrayList<>();

    for (int i = 0; i < 100; i++) {
        elements.add(i);
    }

		// parallelStream()으로 병렬처리를 하고 있다.
    List<Integer> matched = elements.parallelStream()
											        .filter(e -> e >= 90)
											        .collect(Collectors.toList());

    System.out.println(matched);
}

// 결과
10
[90, 91, 92, 93, 94, 95, 96, 97, 98, 99]
```

`collect()`를 통해 병렬 스트림에서의 스트림 연산을 스레드 안전하게 사용할 수 있다.

→ `collect()` 종단 연산은 **Stream** **연산을 객체 하나로 수집한다는 의미의 `Collectors` 클래스**를 활용할 수 있다.

## Stream Collector

스트림을 올바르고 안전하게 사용하기 위한 **축소(reduction) 전략을 캡슐화한 블랙박스 객체**

> ⭐
> **축소**: 스트림의 원소들을 객체 하나에 취합하는 것을 의미.


```java
// toList를 이용하여이름 List를 반환한다.
static List<String> collectToList(final List<User> users) {
    return users.stream()
            .map(User::getName)
            .collect(toList());
}

// toCollection을 이용하여 원하는 Collection을 반환한다.
static Set<User> collectToSet(final List<User> users) {
    return users.stream()
            .sorted(comparingInt(User::getAge))
            .collect(toCollection(TreeSet::new));
}

// 동명이인을 Map으로 grouping 할 수 있다.
static Map<String, Integer> groupingToMap(final List<User> users) {
    return users.stream()
            .collect(groupingBy(User::getName, summingInt(User::getAge)));
}

// downStream(나이 비교 최대값)을 이용하여 Map을 만들 수 있다.
static Map<String, User> collectorsToMap(final List<User> users) {
    return users.stream()
            .collect(toMap(User::getName, user -> user, BinaryOperator.maxBy(comparingInt(User::getAge))));
            
// 이름을 문자열로 이어붙인다.
static String joiningCollection(final List<User> users) {
    return users.stream()
            .map(User::getName)
            .collect(joining(", "));
}

// true / false로 파티셔닝 한다.
static Map<Boolean, List<User>> partitioningByFilter(final List<User> users) {
    return users.stream()
            .collect(partitioningBy(user -> user.getAge() > 25));
}            
```

## 핵심 정리

- 스트림 파이프라인 프로그래밍의 핵심은 부작용 없는 함수 객체에 있다.
- 스트림뿐 아니라 스트림 관련 객체에 건네지는 모든 함수 객체가 부작용이 없어야 한다.
- 종단 연산 중 forEach는 스트림이 수행한 계산 결과를 보고할 때만 이용해야 한다.
- 스트림을 올바로 사용하려면 수집기를 잘 알아둬야 한다.
- 가장 중요한 수집기 팩터리는 toList, toSet, toMap, groupingBy, joining이다.