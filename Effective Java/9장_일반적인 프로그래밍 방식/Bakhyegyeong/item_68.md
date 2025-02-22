# 아이템 68 : 일반적으로 통용되는 명명 규칙을 따르라

특별한 이유없이 패키지, 클래스, 인터페이스, 메서드, 필드, 타입 변수의 **철자 규칙을 무시하지 말아야 한다.**

→ API를 사용하기 어렵고 유지보수가 힘들며 팀원들 간 오해를 야기할 수 있다.

→ 자바 언어의 명명 규칙은 **철자와 문법** 2가지로 나뉜다.

# 철자 규칙

## 패키지와 모듈 이름

1. 패키지와 모듈 이름은 `.` 으로 구분하여 **계층적으로 짓는다.**
2. 만약 조직 바깥에서도 사용될 패키지라면, **조직의 인터넷 도메인 이름을 역순**으로 사용한다.
    
    ex) `com.google`
    
3. 보통 인터넷 도메인 이름 뒤에 요소 하나만 붙인 패키지가 많은데, `java.util`과 같은 많은 기능을 제공하는 경우엔 **계층을 나눠 더 많은 요소로 구성 가능**하다.
    
    ex) `java.util.concurrent.atomic` → 이를 `subpackage`라고 한다.
    
4. 각 패키지 이름은 소문자를 사용한 8자 이하의 짧은 단어 혹은 **약어**를 추천한다.
    - `utilities` → `util`
    - `abstract window toolkit` → `awt`
5. 표준 라이브러리와 선택적 패키지들은 각각 `java`와 `javax`로 시작한다.
6. 요소들은 모두 소문자 알파벳으로 이루어진다.

## 클래스와 인터페이스 이름

1. 열거 타입과 애너테이션도 클래스 혹은 인터페이스로 여긴다.
2. 하나 이상의 단어로 이루어지며, 각 단어는 **대문자로 시작**해야 한다.
    
    ex) `List`, `FutureTask`
    
3. 단어의 첫 글자만 딴 약자나 `max`, `min` 처럼 널리 통용되는 줄임말을 제외하고 **단어를 줄여 쓰면 안된다.**
4. 약자의 경우 **첫 글자만 대문자**로 쓰는 것을 권장한다.
    
    ex) `HttpUrl` vs `HTTPURL`
    
    → 전체를 대문자로 하면 어디서 끊어 읽어야 하는지에 대한 구분이 어렵다.
    

## 메서드와 필드 이름

**첫 글자를 소문자**로 쓴다는 점만 빼면 클래스 명명 규칙과 같다. 

→ 만약 첫 단어가 약자라면, 단어 전체가 소문자여야 한다.

ex) `remove()`, `ensureCapacity()`

## 상수 필드

상수 필드는 예외적으로 모두 **대문자**로 쓰며 단어 사이는 밑줄로 구분한다.

→ 상수란 `static final` 필드를 칭하며 필드의 타입이 기본이거나 **불변 참조 타입**을 의미한다.

ex) `NEGATIVE_INFINITY`

## 지역 변수

1. 다른 멤버와 비슷한 명명 규칙이 적용되지만 **약어를 사용**할 수 있다.
    
    → 변수가 사용되는 문맥에서 의미가 쉽게 유추 가능할 경우에 가능하다.
    
2. **입력 매개변수 또한 지역변수**여서 동일하게 적용가능하지만 메서드 설명 문서에 작성해야하므로 **일반 지역번수보다는 신경**을 써주어야 한다.

## 타입 매개변수

- `T`: 임의의 타입
- `E`: 컬렉션 원소의 타입
- `K, V`: 맵의 키와 값
- `X`: 예외
- `R`: 메서드의 반환 타입
- `T, U, V`, `T1, T2, T3`: 임의의 타입 시퀀스

# 문법 규칙

철자 규칙보다 더 유연해서 논란이 많다.

## 클래스와 인터페이스

1. 객체를 생성할 수 있는 **클래스(열거타입 포함)라면, 단수명사 혹은 명사구를 사용**한다.
    
    ex) `Thread`, `PrioirtyQueue`, `ChessPiece`
    
2. 객체를 생성할 수 없는 클래스의 이름은 보통 복수형 명사로 짓는다.
    
    ex) `Collectors` , `Collections`
    
3. 인터페이스 이름은 클래스와 똑같거나 `able` 혹은 `ible` 로 끝나는 형용사로 짓는다.
    
    ex) `Runnable`, `Iterable`,  `Accessible` , `Closeable`
    

## 애너테이션

애너테이션은 명사, 동사, 전치사, 형용사가 두루 쓰인다.

ex) `Inject`, `ImplementedBy`

## 메서드 이름

1. 동작을 수행하는 메서드의 이름은 동사나 동사구로 짓는다.
    
    ex) `append`, `drawImage`
    
2. `boolean` 값을 반환하는 메서드 
    - `is`나 `has`로 시작
    - 명사, 명사구, 형용사로 기능하는 아무 단어나 구로 끝난다.
    
    ex) `isDigit`, `hasSiblings`
    
3. 반환 타입이 boolean이 아니고 해당 인스턴스의 속성을 반환하는 메서드
    - 명사, 명사구, get으로 시작하는 동사구
    - 때때로 get 을 안 쓰고 단순 명사로 짓는 것이 더 가독성이 좋다.
    
    ex) `size`, `getTime`
    
4. 객체의 타입을 바꿔서 **다른 타입의 객체를 반환**하는 인스턴스 메서드
    - `toType` 형태로 짓는다.
    
    ex) `toString`, `toArray`
    
5. 객체의 내용을 다른 뷰로 보여주는 메서드
    - `asType` 형태로 짓는다.
    
    ex) `asList` 
    
6. 객체의 값을 기본 타입 값으로 반환하는 메서드
    - `typeValue` 형태로 짓는다.
    
    ex) `intValue`
    
7. 정적 팩터리
    - `from`, `of` 등의 네이밍으로 짓는다.
    
    ex) `rom`, `of`, `valueOf`, `instance`, `getInstance`
    

## 필드 이름

필드 이름에 관한 문법 규칙은 클래스, 인터페이스, 메서드 이름에 비해 덜 명확하고 덜 중요하다. 

→ API 설계를 잘 했다면 필드가 **직접 노출될 일이 거의 없기 때문**이다.

1. `boolean` 타입 : `boolean` 접근자 메서드에서 앞 단어를 뺀 형태
    
    ex) `initialized`, `composite`
    
2. 이외의 타입 : 명사, 명사구 사용
    
    ex) `height`, `digits`, `bodyStyle`
    

## 핵심 정리

- 표준 명명 규칙을 활용하여 자연스럽게 베어나오도록 하자.
- 철자 규칙은 직관적이라 모호한 부분이 적은데 반해 문법 규칙은 더 복잡하고 느슨하다.
- 오랫동안 따라온 규칙과 충돌한다면 그 규칙을 맹신하지 말고 상식이 이끄는대로 따르자.