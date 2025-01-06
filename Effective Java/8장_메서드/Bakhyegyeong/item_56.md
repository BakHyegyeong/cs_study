# 아이템 56 : 공개된 API 요소에는 항상 문서화 주석을 작성하라

# API 문서화

자바에서는 **자바독(Javadoc)** 이라는 유틸리티를 지원하고 있다.

→ 소스코드 파일에서 **문서화 주석이라는 특수한 형태**로 기술된 설명을 추려 **API 문서로 변환**해준다.

- `{@literal}` : 주석 내에 HTML 요소나 다른 자바독 태그를 무시
    
    → 글자 그대로 쓸 때 사용 ex) `<`, `>`, `&`
    
- `{@code}` : 감싼 내용을 코드용 폰트로 렌더링
    
    → 여러 줄로 된 코드 예시로 넣으려면 `<pre>{@code index==1}</pre>` 형태로 사용한다.
    
- `@implSpec` : 해당 메서드와 하위 클래스 사이의 계약을 설명
    
    → 하위 클래스들이 그 메서드를 상속하거나 super 키워드를 이용해 호출할 때 **그 메서드가 어떻게 동작하는지 명확히 인지하고 사용할 수 있도록 도와주는 역할**
    

## 문서화 주석의 기본

1. 모든 공개된 클래스, 인터페이스, 메서드, 필드 선언에 문서화 주석을 달아야 한다.
2. 직렬화, 동기화에 대한 내용은 일단 담아야 한다.
3. 메소드용 문서화 주석에서는 **해당 메서드와 클라이언트 사이의 규약**을 명료하게 기술해야 한다.
    - 메서드의 동작 과정이 아닌 아닌 무엇을 하는지 기술
    - `@param`, `@return`, `@throws` 로 파라미터, 반환값, 예외를 명시
    - 클라이언트가 해당 메서드를 호출하기 위한 전제조건 나열
    - 메서드가 수행된 후에 만족해야 하는 사후조건 나열
    - 시스템 상태에 어떠한 변화를 가져오는 부작용 기술 나열
        
        ex) 백그라운드 스레드
        

## 메서드 주석

```java
/**
 * Returns the element at the specified position in this list.  // 요약 설명
 *
 * <p>This method is <i>not</i> guaranteed to run in constant
 * time. In some implementations it may run in time proportional
 * to the element position.
 *
 * @param  index index of element to return; must be
 *         non-negative and less than the size of this list
 * @return the element at the specified position in this list
 * @throws IndexOutOfBoundsException if the index is out of range
 *         ({@code index < 0 || index >= this.size()})
 */
E get(int index) {
    return null;
}
```

- 모든 매개변수에 `@param` 태그
- 반환 타입이 void가 아니라면 `@return` 태그
- 발생할 가능성이 있는 모든 예외(검사/비검사)에 `@throws` 태그
- `@태그`의 설명에는 문장보다는 명사구를 쓰는 것이 좋으며 보통 마침표를 붙이지 않는다.
- 영문 주석의 this는 호출된 메서드가 자리하는 객체를 가리킨다.

### 요약 설명

각 문서화 주석의 첫 번째 문장

- 대상의 **고유한 기능을 기술**해야 한다.
    
    → **한 클래스 안에서 요약 설명이 똑같은 멤버 혹은 생성자가 둘 이상이면 안된다.**
    
- **마침표**를 기준으로 구분된다.
    
    → 설명 내에 마침표를 사용해야 한다면 `{@literal}` 로 감싸주어 의도치 않은 마침표로 인한 혼동을 해결할 수 있다.
    
    ```java
    /**
    * A suspect, such as Colonel Mustard or {@literal Mrs. Peacock}.
    */
    public enum Suspect {...}
    ```
    

## 제네릭 타입의 문서화 주석

제네릭 타입이나 제네릭 메서드를 문서화 할때는, 모든 **제네릭** **타입 매개변수**에 주석을 달아야 한다.

```java
/**
* An object that maps keys to values. A map cannot contain duplicate keys; each key can map to at most one value.
*
* (Remainder omitted)
*
* @param <K> the type of keys maintained by this map
* @param <V> the type of mapped values
*/
public interface Map<K, V> {...}

```

## 열거 타입의 문서화 주석

열거 타입을 문서화할 때는 **상수**들에도 주석을 달아야 한다.

```java
/**
* An instrument section of a symphony orchestra.
*/
public enum OrchestraSection {
    /** Woodwinds, such as flute, clarinet, and oboe. */
    WOODWIND,

    /** Brass instruments, such as french horn and trumpet. */
    BRASS,

    /** Percussion instruments, such as timpani and cymbals. */
    PERCUSSION,

   /** Stringed instruments, such as violin and cello. */
    STRING;
}
```

## 애너테이션의 문서화 주석

애너테이션 타입을 문서화할 때는 멤버들에도 모두 주석을 달아야 한다.

→ 애너테이션 타입의 요약 설명에서는, **이 애너테이션을 단다는 것이 어떤 의미인지를 설명**하면 된다.

```java
/**
* Indicates that the annotated method is a test method that
* must throw the designated exception to pass.
*/
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface ExceptionTest {
    /**
    * The exception that the annotated test method must throw
    * in order to pass. (The test is permitted to throw any
    * subtype of the type described by this class object.)
    */
    Class<? extends Throwable> value();
}
```

## 문서화 주석의 검색 기능

자바 9 이후부터는, 자바독이 생성한 HTML 문서에 검색(색인) 기능이 추가되었다.

- 클래스, 메서드, 필드 같은 API 요소의 색인은 자동으로 만들어진다.
    
    → 원한다면 `{@Index}` 태그를 사용해 API에서 중요한 용어를 추가로 색인화할 수 있다.
    
    ```java
    /**
    *This method complies with the {@index IEEE 754} standard.
    **/
    public void fragment2() {...}
    ```
    
- 문서화 주석이 없는 API 요소를 발견하면, 자바독이 가장 가까운 문서화 주석을 찾아준다. <br>
→ 상위 '클래스'보다 클래스가 구현한 '인터페이스'를 먼저 찾는다.
- `{@inheritDoc}` 태그를 사용해 상위 타입의 문서화 주석 일부를 상속할 수 있다. <br>
→ 클래스는 자신이 구현한 인터페이스의 문서화 주석을 재사용할 수 있다.

## 핵심 정리

문서화 주석은 API를 문서화하는 가장 훌륭하고 효과적인 방법이다. 

공개 API라면 모두 설명을 달아야 하며, 표준 규약을 일관되게 지키자.

문서화 주석에 임의의 HTML 태그를 사용할 수 있음을 기억하라. 단, HTML 메타문자는 특별하게 취급해야 한다.