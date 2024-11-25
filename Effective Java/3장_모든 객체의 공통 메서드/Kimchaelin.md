# 3장 모든 객체의 공통 메서드
## 아이템 10. equals는 일반 규약을 지켜 재정의하라
- 재정의 하지 않아도 되는 경우
  - 각 인스턴스가 본질적으로 고유하다.
  - 값을 표현하는 것이 아니라 개체를 표현하는 경우.
    ```java
              // Person 클래스는 값을 표현하는 것이 아닌 개체를 표현하는 것이기 때문에 equals를 재정의할 필요가 없다.
              public class Person {
                  private int age;
                  private String name; 
              }
    ```
  - 인스턴스의 '논리적 동치성(logical equality)'을 검사할 일이 없다.
    - 논리적 동치성 : 두 객체가 같은 논리적 개체를 나타내는지 검사하는 것
    - 논리적 동치성을 검사하지 않아도 되면 객체 식별성을 검사하는 기존 equals를 사용하면 된다.
  - 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
    - 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
      - private인 클래스는 호출이 불가능하므로 equals를 재정의할 필요가 없다. 
- equals를 재정의해야 하는 경우
  - 객체 식별성(object identity)이 아닌 논리적 동치성을 확인해야 하는 경우
    - 객체 식별성 : 두 객체가 물리적으로 같은 객체인지 검사하는 것
  - 상위 클래스에서 equals를 재정의하지 않았을 때
    - 상위 클래스의 equals가 객체 식별성을 검사하도록 구현되어 있을 때
- Equals 메서드를 재정의할 때 지켜야 할 일반 규약
  - 반사성(reflexivity) : x.equals(x)는 true
  - 대칭성(symmetry) : x.equals(y)가 true면 y.equals(x)도 true
  - 추이성(transitivity) : x.equals(y)가 true이고 y.equals(z)도 true면 x.equals(z)도 true
  - 일관성(consistency) : x.equals(y)를 반복해서 호출하면 항상 true나 false 중 하나를 반환
  - null-아님 : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.
- equals 메서드 구현 방법 단계
  1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인
  2. instanceof 연산자를 사용해 입력이 올바른 타입인지 확인.
  3. 2를 통해 검사한 입력을 올바른 타입으로 형변환
  4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사
  5. null인지 체크
  ```java
  @Override
  public boolean equals(Object o) {
    if (o == this) {// 1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인
        return true;
    }
    if (!(o instanceof PhoneNumber)||o==null) {// 2. instanceof 연산자를 사용해 입력이 올바른 타입인지 확인. // 5. null인지 체크
        return false;
    }
    PhoneNumber pn = (PhoneNumber) o;// 3. 2를 통해 검사한 입력을 올바른 타입으로 형변환
    return pn.lineNumber == lineNumber && pn.prefix == prefix && pn.areaCode == areaCode;// 4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사
  }
  ```
## 아이템 11. equals를 재정의하려거든 hashCode도 재정의하라
- equals가 같다고 판단되는 두 객체는 같은 hashCode를 반환해야 한다.
  - 같지 않으면 HashMap, HashSet과 같은 컬렉션의 성능이 떨어진다. 같은 객체인데 해시 값이 다르면 다른 버킷에 담기기 때문이다.
- hashcode 작성법
  1. int 변수 result를 선언한 후, 첫 번째 핵심 필드 f의 해시코드를 계산하여 result를 초기화
  2. f의 나머지 핵심 필드 c에 대해 다음 작업을 반복
     - 해시코드를 계산
     - 해당 해시코드 값으로 result를 갱신. => result = 31 * result + c의 해시코드 (31은 홀수이면서 소수)
  3. result를 반환
- 규약 
  - equals를 재정의한 클래스 모두에서 hashCode를 재정의해야 한다.
  - equals가 두 객체를 같다고 판단하면 두 객체의 hashCode는 같아야 한다.
  - equals가 두 객체를 다르다고 판단해도, 두 객체의 hashCode가 서로 다를 필요는 없다.

## 아이템 12. toString을 항상 재정의하라
- toString을 재정의하지 않으면 클래스 정보가 출력되지 않는다.
  - ex) PhoneNumber{areaCode=707, prefix=867, lineNumber=5309} -> PhoneNumber@adbbd(클래스 이름@16진수로 표현된 해시코드로 표시됨)
- toString을 재정의하는 이유
  - 객체가 가진 주요 정보를 모두 반환해서 toString을 재정의하면 사용자가 읽기 쉬운 형태로 객체를 출력할 수 있다.
  ```java
  // 어래와 같이 toString 메서드를 재정의하면 PhoneNumber{areaCode=707, prefix=867, lineNumber=5309}와 같이 출력된다.
    @Override public String toString() {
        return String.format("%03d-%03d-%04d",
                             areaCode, prefix, lineNumber);
    }
  ```
- 로그를 찍을 일이 있을 거 같으면 toString을 재정의하자.
- 전부 재정의할 필요는 없다. 중요한 정보만 출력하면 된다.

## 아이템 13. clone 재정의는 주의해서 진행하라
- clone 메서드는 Cloneable 인터페이스를 구현한 클래스에서만 사용할 수 있다.
  - Cloneable 인터페이스는 clone 메서드를 사용할 수 있게 해주는 마커 인터페이스이다.
  - Cloneable 인터페이스를 구현하지 않은 클래스의 clone 메서드를 호출하면 CloneNotSupportedException이 발생한다.
  - Cloneable 인터페이스는 Object의 protected 메서드인 clone을 public으로 바꾸는 역할만 한다.
  - 새로운 인터페이스를 만들 때 Cloneable을 확장해서는 안된다. 새로운 클래스도 이를 구현해서는 안된다.(문제가 많다.)
```java
//배열 복사
int[] arr = {1, 2, 3};
int[] arr2 = arr; // 얕은 복사
int[] arr3 = arr.clone(); // 깊은 복사
```

```java
//복사시, 주의사항
Book[] arr = new Book[]{new Book("a"), new Book("b")};
Book[] arr2 = arr.clone();
arr2[0].setTitle("c");
// Object의 reference를 복사하기 때문에 arr[0]의 title도 c로 바뀐다. 즉, 둘이 같은 객체를 가리키고 있다.
```
- 일반 규약
  - x.clone() != x
  - x.clone().getClass() == x.getClass() -> 일반적으로 참이지만 필수는 아니다.
  - x.clone().equals(x)
- 객체 복사 방식
  - 복사 생성자 : 객체를 복사하는 생성자
  - 복사 팩터리 : 객체를 복사하는 정적 팩터리
  - 위에 방식들이 Cloneable/clone 방식보다 나은 면이 많다.
  - 단, 배열만은 clone이 더 좋다.

## 아이템 14. Comparable을 구현할지 고려하라
- 객체끼리의 순서를 정할 수 있게 해주는 인터페이스
- 해당 객체가 주어진 객체보다 작으면 음수, 같으면 0, 크면 양수를 반환한다.
- 비교할 수 없을 땐 ClassCastException을 던진다.
- compareTo 메서드의 일반 규약
  - x.compareTo(y) == -y.compareTo(x)
  - x.compareTo(y) > 0 && y.compareTo(z) > 0 이면 x.compareTo(z) > 0
  - x.compareTo(y) == 0 이면 x.compareTo(z) == y.compareTo(z)
  - (x.compareTo(y) == 0) == (x.equals(y))
- equals vs compareTo
  - equals : 동치성 비교
  - compareTo : 순서 비교
- compareTo 메서드를 재정의할 때 주의할 점
  - compareTo 메서드에서 필드를 비교할 때 <, > 연산자를 사용하면 안된다.
  - 박싱된 기본 타입 클래스의 compareTo 메서드를 사용하자.
- Comparable을 구현한 클래스는 compareTo 메서드를 재정의해야 한다.
  ```java
    public class PhoneNumber implements Comparable<PhoneNumber> {
        @Override
        public int compareTo(PhoneNumber pn) {
            int result = Short.compare(areaCode, pn.areaCode);
            if (result == 0) {
                result = Short.compare(prefix, pn.prefix);
                if (result == 0) {
                    result = Short.compare(lineNumber, pn.lineNumber);
                }
            }
            return result;
        }
    }
  ```
  ```java
    // 자바 8 이후 - 비교자 생성 메서드를 활용한 compareTo 메서드
    public class PhoneNumber implements Comparable<PhoneNumber> {
        private static final Comparator<PhoneNumber> COMPARATOR =
            comparingInt((PhoneNumber pn) -> pn.areaCode)
                .thenComparingInt(pn -> pn.prefix)
                .thenComparingInt(pn -> pn.lineNumber);

        @Override
        public int compareTo(PhoneNumber pn) {
            return COMPARATOR.compare(this, pn);
        }
    }
  ```
- 값의 차이를 반환하는 방식
  ```java
  // 추이성을 위배하므로 좋지 않은 방식 - 추이성이란 x.compareTo(y) > 0 && y.compareTo(z) > 0 이면 x.compareTo(z) > 0
    public int compareTo(int a, int b) {
        return a - b;
    }
  ```
  ```java
  // 올바른 방식
    public int compareTo(int a, int b) {
        return Integer.compare(a, b);
    }
    ```
  ```java
    import java.util.Comparator;// 자바 8 이후 - 비교자 생성 메서드를 활용한 compareTo 메서드
    public int compareTo(int a, int b) {
        return Comparator.comparingInt(a, b);
    }
  ```
