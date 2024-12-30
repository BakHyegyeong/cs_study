# 아이템 45 : 스트림은 주의해서 사용하라

# 스트림

다량의 데이터 처리 작업(순차/병렬)을 돕고자 나온 API

→ 원하는 결과를 생성하기 위해 **파이프라인으로 연결될 수 있는 다양한 메서드를 지원**하는 개체 시퀀스이다.

- 대용량 데이터 처리를 위한 오토박싱/언박싱 과정이 필요없는 Instream 같은 기본 스트림도 제공한다.
- 스트림이 흘러가는 과정에서 데이터가 순차적으로 하나씩 사용되고 사라진다.
    
    

> 💡 **stream과 stream pipeline**
>
> - **스트림(stream)**: 데이터 원소의 유한 혹은 무한 시퀀스
> - **스트림 파이프라인(stream pipeline)**: 이 원소들로 수행하는 연산 단계
>
> ---
>
> 💡 **시퀀스**
>
> - 데이터 요소의 연속된 집합
> - → 배열이나 리스트 같은 데이터 구조

## 스트림 파이프라인 연산 과정

소스 스트림 → 중간 연산(하나 이상) → 종단 연산의 과정으로 수행된다.

```java
List<Integer> transactionsIds = 
    transactions.stream()
                .filter(t -> t.getType() == Transaction.GROCERY)
                .sorted(comparing(Transaction::getValue).reversed())
                .map(Transaction::getId)
                .collect(toList());
```

### 중간 연산

스트림을 어떠한 형식으로 변환하는 역할

→ 한 스트림을 다른 스트림으로 변환해 메서드 체이닝을 가능하게 한다.

ex) `filter()`, `map()`, `sorted()` : 각 원소에 함수를 적용하거나 특정 조건을 만족 못하는 원소를 걸러내는 연산

> 💡 **메서드 체이닝**
> 
> 여러 메서드 호출을 연결하여 호출하는 프로그래밍 기술
>
> → 각 메서드는 연산 후에 그 결과를 반환하는 객체를 반환한다.
>
> ### 사용 예시
>
> ```java
> public class Person {
>     private String name;
>     private int age;
> 
>     public Person setName(String name) {		
>         this.name = name; 		
>         return this; // 자신 객체 반환
>     } 	
> 
>     public Person setAge(int age) { 		
>         this.age = age;
>         return this; // 자신 객체 반환
>     }
> }
> 
> // 사용 예시
> new Person().setName("bob").setAge(14);
> ``` 

### 종단 연산

마지막 중간 연산이 내놓은 스트림에 마지막 연산을 가하는 역할

ex) `forEach()`, `collect()`, `match()`, `count()`, `reduce()` : 원소를 정렬해 컬렉션에 담거나 특정 원소를 하나 선택하는 연산

## 스트림 파이프라인 특징

### 지연 평가

**결과값이 필요할 때까지 계산을 늦추는 기법**

→ 대용량 데이터에서 실제로 **필요하지 않은 데이터들을 탐색하는 것을 방지**해 속도를 높일 수 있다.

→ 종단 연산에 쓰이지 않는 데이터 원소는 계산을 하지 않는다.

- `limit(n)` 사용

```java
@Test
void streamLazyTest() {
    System.out.println("=== using short-circuit operation ===");
    List<String> limitList = List.of("abcde", "adceb", "dcabeda", "gsefds", "aa");
    
    limitList.stream()
        .filter(x -> x.length() >= 5)
        .peek(x -> System.out.println("intermediate: " + x))
        .limit(2) // 최대 2개의 요소만 처리
        .forEach(x -> System.out.println("terminate: " + x));
}

// 출력 결과
=== using short-circuit operation ===
intermediate: abcde
terminate: abcde
intermediate: adceb
terminate: adceb
```

`limit(n)` 연산이 내부적으로 **자신에게 도달한 요소가 `n` 개가 되었을 때** 스트림 내 다른 요소들에 대해 더 이상 **순회하지 않고 탈출**하도록 한다.

→ 이 특성으로 무한 스트림을 다룰 수 있게 된다.

- 무한 스트림
    - 유한 스트림 : 생성할 때 크기가 정해져 있는 스트림
        
        ```java
        IntStream ints(long streamsize, int begin, int end)
        ```
        
    - 무한 스트림 : 무한한 크기로 값을 가지는 스트림
        
        ```java
        IntStream ints(int begin, int end)
        ```
        
    
    무한 스트림에 `limit()`과 같은 `short-circuit` 연산을 통해 유한 스트림으로 변환함으로써 **중복 제거가 가능해진다.**
    

### 지연 평가와 Stateful 연산

중복을 제거하는 `distinct()` 나, 전체 데이터를 정렬하는 `sort()` 연산들은 **지연 평가를 무효화하고 결과를 생성하기 전에 전체 데이터를 탐색하는 결과를 초래**한다.

```java
@Test
void streamLazySortTest() {
    System.out.println("\n=== using short-circuit operation + sort => no lazy ===");
    List<String> noList = List.of("abcde", "adceb", "dcabeda", "gsefds", "aa");
    
    noList.stream()
        .filter(x -> x.length() >= 5)
        .peek(x -> System.out.println("intermediate: " + x))
        .sorted() // 정렬 수행
        .limit(2) // 최대 2개의 요소만 처리
        .forEach(x -> System.out.println("terminate: " + x));
}

// 출력 결과
=== using short-circuit operation + sort => no lazy ===
intermediate: abcde
intermediate: adceb
intermediate: dcabeda
intermediate: gsefds
terminate: abcde
terminate: adceb
```

`.sorted()`는 **전체 요소를 정렬**하기 때문에 지연 평가가 적용되지 않는다.

→ `sorted()`는 `peek()`와 `limit()` 메서드 이전에 실행된다.


> 💡
> - Short-Circuit : 불필요한 연산을 생략하는 방법 <br>
>    ex) limit() <br><br>
> - Stateful 연산 : 연산이 어디까지 진행되었는지 기억해야하는 연산 <br>
>    ex) distinct(), sort()


### 순차성

스트림 파이프라인은 순차적으로 수행한다.

→ 파이프라인을 병렬로 수행하려면, 파이프라인을 구성하는 스트림 중 하나에서 `parallel` 메서드를 호출해 사용한다.

## 스트림의 적절한 활용

사전 파일에서 단어를 읽어 사용자가 지정한 문턱값보다 원소 수가 많은 아나그램 그룹을 출력하는 코드이다.

### 스트림을 사용하지 않은 예시

```java
public class Anagrams {
	public static void main(String[] args) throws IOException {
		File dictionary = new File(args[0]); // 사전 파일
		int minGroupSize = Integer.parseInt(args[1]); // 사용자가 지정한 원소 수 문턱값

		// key : 알파벳 순으로 정렬한 값, value : 같은 키를 공유한 단어들을 담은 집합
		Map<String, Set<String>> groups = new HashMap<>();

		try (Scanner s = new Scanner(dictionary)) { //사전 파일에서 단어 읽음
			while (s.hasNext()) {
				String word = s.next();
				// 
				groups.computeIfAbsent(alphabetize(word), (unused) -> new TreeSet<>()).add(word);
			}
		}

		//minGroupSize 보다 원소 수가 많은 아나그램 그룹 출력
		for (Set<String> group : groups.values()) {
			if (group.size() >= minGroupSize) {
				System.out.println(group.size() + " : " + group);
			}
		}
	}

	// 아나그램 그룹의 key를 만들어줌
	private static String alphabetize(String s) {
		char[] a = s.toCharArray();
		Arrays.sort(a);
		return new String(a);
	}
}
```

### 과한 스트림 사용

```java
public class Anagrams {
	public static void main(String[] args) throws IOException {
		Path dictionary = Paths.get(args[0]); // 사전 파일 경로
		int minGroupSize = Integer.parseInt(args[1]); // 사용자가 지정한 원소 수 문턱값

		try (Stream<String> words = Files.lines(dictionary)) { // try-with-resources 문을 사용해 파일을 닫음
			
			words.collect(
					groupingBy(word -> word.chars().sorted()
							.collect(StringBuilder::new,
									(sb, c) -> sb.append((char) c),
									StringBuilder::append).toString()))
					.values().stream()
					.filter(group -> group.size() >= minGroupSize) //
					.map(group -> group.size() + " : " + group)
					.forEach(System.out::println);
		}
	}
}
```

사전 파일을 여는 부분을 제외하고 프로그램 전체가 단 하나의 표현식으로 처리되고 있다.

→ **코드 자체는 간결해졌지만 읽거나 유지보수하기에 어렵다.**

### 적절한 스트림 활용

```java
public class Anagrams {
	public static void main(String[] args) throws IOException {
		Path dictionary = Paths.get(args[0]); // 사전 파일 경로
		int minGroupSize = Integer.parseInt(args[1]); // 사용자가 지정한 원소 수 문턱값

		try (Stream<String> words = Files.lines(dictionary)) { // try-with-resources 문을 사용해 파일을 닫음
			words.collect(groupingBy(Anagrams::alphabetize)) // alphabetize 메서드로 단어들을 그룹화함
                .values().stream()
                .filter(group -> group.size() >= minGroupSize) // 문턱값보다 작은 것을 걸러냄
                .forEach(group -> System.out.println(group.size() + " : " + group)); // 필터링이 끝난 리스트 출력
		}
	}

	// 아나그램 그룹의 key를 만들어줌
	private static String alphabetize(String s) {
		char[] a = s.toCharArray();
		Arrays.sort(a);
		return new String(a);
	}
}
```

- 적절한 스트림 활용으로 코드가 간결해질 뿐만 아니라 명확해졌다.
- `alphabetize()`과 같은 세부 구현을 분리해 가독성을 높이고 도우미 메서드로 활용하였다.
- 람다에서는 타입 이름을 자주 생략하므로, 매개변수의 이름을 잘 지어야 스트림 파이프라인의 가독성이 유지할 수 있다.

## 스트림 파이프라인과 반복 코드 비교

| **특징** | **스트림 파이프라인** | **반복 코드** |
| --- | --- | --- |
| **계산 표현 방식** | 함수 객체를 사용하여 계산을 표현 | 코드 블록을 사용하여 계산을 표현 |
| **지역 변수 접근** |  `final`이거나 사실상 `final`인 변수만 읽을 수 있음 |  코드 블록 범위 안의 지역 변수를 자유롭게 읽을 수 있음 |
| **지역 변수 수정 가능 여부** | 지역 변수 수정 불가능 | 지역 변수 수정 가능 |
| **제어문 사용** |  `return`, `break`, `continue` 등을 사용할 수 없음 |  `return`으로 메서드 종료, `break` 또는 `continue`로 반복문 제어 가능 |
| **코드 간결성** | 간결하고 선언적 | 더 많은 코드 작성 필요 |
| **병렬 처리 지원** | 병렬 스트림을 사용하여 쉽게 병렬 처리 가능 | 직접 병렬 처리를 구현해야 하며 복잡함 |

## 스트림을 사용하기에 적절한 경우

1. 원소들의 시퀀스를 일관되게 변환해야 하는 경우 : `map()`
2. 원소들의 시퀀스를 필터링 해야 하는 경우 : `filter()`
3. 원소들의 시퀀스를 하나의 연산을 사용해 결합해야 하는 경우(더하기, 연결하기, 최솟값 구하기 등) : `reduce()`
4. 원소들의 시퀀스를 컬렉션에 모으는 경우(공통된 속성을 기준으로) : `collect()`
5. 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾을 경우 : `filter()`

## 스트림을 사용하기 어려운 경우

1. 파이프라인의 각 단계에서의 값들에 동시에 접근해야하는 경우
    
    → 파이프라인은 일단 한 **값을 다른 값에 매핑하고 나면 원래의 값은 잃는 구조이기 때문**이다.
    
2. char 값을 처리할 경우
    
    자바는 기본 타입인 `char` 용 스트림을 지원하지 않는다. 
    
    → `boolean[]`, `byte[]`, `short[]`, `char[]`, `float[]` 도 해당 타입의 기본형 스트림이 존재하지 않는다.
    

## 핵심 정리

스트림과 반복 중 어느쪽이 나은지 확신하기 어렵다면, 둘 다 테스트해보고 더 나은 쪽을 택하는 것이 좋다.