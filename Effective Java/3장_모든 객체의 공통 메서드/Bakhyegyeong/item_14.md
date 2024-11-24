# 아이템 14 : Comparable을 구현할지 고려하라

# Comparable

: 구현한 객체가 **자연적인 순서(natural order)가 있음을 명시**하고, `compareTo`를 통해 이 순서에 따른 정렬을 가능하게 한다.

- `Comparable` 인터페이스를 구현한 클래스는 반드시 `compareTo`를 정의해야 한다.
- 알파벳, 숫자와 같이 순서가 명확한 값 클래스를 작성한다면 반드시 `Comparable` 인터페이스를 구현해야 한다.
- 자바에서 같은 타입의 인스턴스를 비교해야만 하는 클래스들은 모두 `Comparable` 인터페이스를 구현하고 있다.
    
    → `Comparable`을 구현한 객체들의 배열은 `Arrays.sort(a)`로 쉽게 정렬이 가능하다.
    

```java
public interface Comparable<T> {

    public int compareTo(T o);
}
```

## compareTo

: 해당 객체와 전달된 객체의 순서를 비교하는 메서드

- `compareTo`는 `Object`의 `euqals`와 두가지 차이점이 있다. `compareTo`는 `equals`와 달리 **단순 동치성에 더해 순서까지 비교할 수 있으며, 제네릭하다.**
- 해당 객체가 주어진 객체보다 **작은 경우 음의 정수**를 반환하고, **같은 경우에는 0**, **큰 경우 양의 정수를 반환**한다.
- 해당 객체와 비교할 수 없는 타입이 주어진다면, ClassCastException을 던진다.

### compareTo 메서드의 일반 규약

1. 대칭성 : 두 객체참조의 순서를 바꿔 비교해도 예상한 결과가 나와야한다.
    - 모든 x, y에 대하여 x.compareTo(y) 와 y.compareTo(x) 부호는 반대여야 한다.
    - x.compareTo(y) 가 0이라면 y.compareTo(x)도 0이어야 한다.
    - x.compareTo(y)는 y.compareTo(x)가 예외를 던질때에 한해 예외를 던져야 한다.
2. 추이성 : 첫 번째가 두번째보다 크고 두 번째가 세번째보다 크면 첫번째는 세번째보다 커야한다.
    - (x.compareTo(y)>0 && y.compareTo(z) > 0)이면 x.compareTo(z) > 0 이다.
3. 반사성 : 크기가 같은 객체들끼리는 어떤 객체와 비교하더라도 항상 같다.
    - x.compareTo(y)==0 이면 x.compareTo(z)와 y.compareTo(z)의 값이 같다.
4. equals : `compareTo` 메서드로 수행한 동치성테스트 결과가 `equals`와 같아야한다.
    - (x.compareTo(y) == 0) == (x.equals(y))여야 한다.
    - 지키지 않는 클래스는 그 사실을 명시해야 한다.

## equals와 compareTo의 차이점

```java
final BigDecimal bigDecimal1 = new BigDecimal("1.0");
final BigDecimal bigDecimal2 = new BigDecimal("1.00");
```

이 둘을 **compareTo로 비교한다면 두 인스턴스를 같은 값**으로 인식하지만 **equals로 비교한다면 다른 값**으로 인식한다.

### compareTo 메서드 작성 팁

- `Comparable`은 타입을 인수로 받는 제네릭 인터페이스이므로 `compareTo`의 인수타입은 컴파일 시에 정해지기 때문에 입력 인수 확인이나 형변환을 할 필요가 없다.
- `null`을 인수로 넣으면 `NullPointerException`을 던져야한다.
- `compareTo`는 동치가 아닌 순서를 비교한다.
- 객체 참조 필드를 비교하려면 `compareTo` 메서드를 재귀적으로 호출한다.
- `Comparable`을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 한다면 `Comparator`을 대신 사용한다.
- **compareTo 메서드에서 관계연산자 (<, >)를 사용하지 않는것을 추천한다.**
    
    → 박싱된 기본 타입 클래스들에 새로 추가된 정적 메서드 `compare`를 대신 이용한다.
    
    ```java
    class MyObject implements Comparable<MyObject> {
        private int a;
        private double b;
    
        @Override
        public int compareTo(final MyObject o) {
            if (a == o.a) {
                return Double.compare(b, o.b);
            }
            return Integer.compare(a, o.a);
        }
    }
    ```
    
- 클래스에 핵심 필드가 여러개라면 가장 핵심적인 필드부터 비교하고 순서가 결정되지 않는다면 똑같지 않은 필드를 찾을 때까지 비교하도록 구현한다.
    
    ```java
    public int compare(final PhoneNumber phoneNumber) {
            int result = Short.compare(areaCode, phoneNumber.areaCode);
    
            if (result == 0) {
                result = Short.compare(prefix, phoneNumber.prefix);
                if (result == 0) {
                    result = Short.compare(lineNum, phoneNumber.lineNum);
                }
            }
    
            return result;
    }
    ```
    

## Comparable과 Comparator의 비교

| / | Comparable | Comparator |
| --- | --- | --- |
| 비교 | 자기 자신과 매개변수 객체 | 두 매개변수 객체 |
| 구현 | 정렬해야 하는 클래스 | 익명 객체 |
| 패키지 | long 패키지 | util 패키지 |

## 핵심 정리

순서를 고려해야하는 값 클래스를 작성한다면 꼭 Comparable

인터페이스를 구현하여 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야한다.

`compareTo`메서드에서 필드의 값을 비교할 때 `<` 와 `>`연산자는 지양해야 한다. 

그 대신 박싱된 기본 타입 클래스가 제공하는 정적 `compare`메서드나 `Comparator`가 제공하는 비교자 생성 메서드를 이용하자.

https://velog.io/@alkwen0996/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C14-Comparable%EC%9D%84-%EA%B5%AC%ED%98%84%ED%95%A0%EC%A7%80-%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC

https://ttl-blog.tistory.com/1230

https://github.com/woowacourse-study/2022-effective-java/blob/main/03%EC%9E%A5/%EC%95%84%EC%9D%B4%ED%85%9C_14/Comparable%EC%9D%84_%EA%B5%AC%ED%98%84%ED%95%A0%EC%A7%80_%EA%B3%A0%EB%A0%A4%ED%95%98%EB%9D%BC.pdf
