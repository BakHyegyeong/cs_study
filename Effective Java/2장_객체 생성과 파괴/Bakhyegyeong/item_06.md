# 아이템 6 : 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매변 생성하기보다는 **객체 하나를 재사용**하는 편이 좋을 때가 많다. 특히 불변 객체는 언제든 재사용할 수 있다.

만약, 무거운 객체라면 **매번 생성할 때마다 많은 자원**이 들어갈 것이고, 인스턴스를 자주 생성하게 되면 가비지 컬렉션이 동작하게 될 확률이 높아지고 이는 **애플리케이션 성능을 저하**시키는 요인 중 하나이다.

## 객체를 재사용해야 하는 이유 - String

```java
// new 연산자를 이용한 방식
String str1 = new String("java");

// 리터럴을 이용한 방식
String str2 = "java";
```

첫번째 코드는 실행될 때마다 `String` 인스턴스를 새로 만든다. 인스턴스를 새로 만들때마다 `heap` 영역에 `String` 인스턴스가 저장이 된다.

두번째 코드는 새로운 인스턴스를 매번 만드는 대신 하나의 `String` 인스턴스를 재사용한다. 문자열 리터럴을 통해서 `heap` 영역의 `String Constant Pool`에 저장되어 인스턴스가 재사용 된다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c8afdb21-c5ae-467f-8cd8-6745a5c4ad5c/e85558fd-14de-4cd3-93f1-03171ed01b6e/image.png)

<aside>
💡

**리터럴** : 프로그램에서 사용하는 모든 숫자, 문자, 문자열, 논리 값 등

프로그램의 로딩 과정에서 사용된 리터럴이 **constant pool에 저장**되고 이후에 사용되는 경우 **constatant pool에서 가져와 변수에 대입/복사**하는 방식으로 사용한다.

https://kkkdh.tistory.com/entry/Java-%EC%9E%90%EB%B0%94-%EA%B3%B5%EB%B6%80-3-%EB%A6%AC%ED%84%B0%EB%9F%B4literal%EC%9D%98-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%9E%90%EB%A3%8C%ED%98%95-%EA%B0%84%EB%8B%A8-%EC%A0%95%EB%A6%AC

</aside>

```java
int x = 3; // 3이라는 int type literal이 변수 x에 복사되어있다.
x = 5; // 5라는 int type literal을 constant pool에서 찾아 변수 x에 할당한다.
```

<aside>
💡

**String Constant Pool** 

: Java에서 문자열 리터럴을 저장하는 독립된 영역

**String은 불변객체**이기 때문에 문자열의 생성 시 이 String Constant Pool에 저장된 리터럴을 재사용

https://deveric.tistory.com/123

</aside>

<aside>
💡

**Constant Pool**

: 컴파일시 클래스파일 내부에 존재하는 영역으로, 클래스로더에 의해 JVM에 로드될 때 메모리에 로드

</aside>

→ **String Constant Pool과 Constant Pool은 다르다.**

## 정적 팩터리 메서드를 통해 불필요한 객체 생성을 방지

**생성자 대신 정적 팩터리 메서드를 제공**(이를 통해 객체 생성 없이 메서드를 사용할 수 있다.)하는 불변 클래스는 정적 팩터리 메서드를 활용해 불필요한 객체 생성을 피할 수 있다.

```java
public final class Boolean implements java.io.Serializable, Comparable<Boolean> {

    public static final Boolean TRUE = new Boolean(true);
    public static final Boolean FALSE = new Boolean(false);
    
    // ...
    public static boolean parseBoolean(String s) {
        return "true".equalsIgnoreCase(s);
    }

    // ...
    public static Boolean valueOf(String s) {
        return parseBoolean(s) ? TRUE : FALSE;
    }
}
```

`Boolean.valueOf(String)`정적 팩터리 메서드를 사용하여 미리 생성된 `TRUE`, `FALSE`를 반환하는 것을 확인할 수 있다.

## **객체 생성이 비싼 경우 캐싱을 통해 객체 생성을 방지**

메모리, 디스크 사용량, 대역폭 등이 높은, **인스턴스를 생성하는데 드는 비용이 큰 객체의 경우 캐싱을 통해 재사용**한다.

```java
static boolean isRomanNumeral(String str) {
    return str.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```

이때 string의 matches메서드는 정규표현식용 `Pattern` 인스턴스를 생성하는데 이 `Pattern`은 입력받은 정규표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 높다.

→ 이 문제를 해결하기 위해 `Pattern` 객체를 만들어 컴파일하고 재사용을 하는 것이 좋다.

```java
public class RomanNumber {
		// Pattern 재활용
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String str) {
        return ROMAN.matcher(str).matches();
	}
}
```

<aside>
💡

정규 표현식 : 특정한 규칙을 가진 문자열의 집합을 표현하는데 쓰이는 형식 언어

→ 전화번호, 주민등록번호, 이메일등과 같이 **정해져있는 형식**이 있고 **사용자가 그 형식대로 제대로 입력했는지 검증**을 해야하는 경우 사용된다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c8afdb21-c5ae-467f-8cd8-6745a5c4ad5c/d855834e-16ee-43f4-b347-f2ebe56f4fe1/image.png)

Pattern : 정규 표현식이 컴파일된 클래스로써 **정규 표현식에 대상 문자열을 검증하는 기능**을 수행한다.

https://velog.io/@kai6666/Java-%EC%A0%95%EA%B7%9C-%ED%91%9C%ED%98%84%EC%8B%9D%EA%B3%BC-Pattern%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%B0%8F-%EB%A9%94%EC%84%9C%EB%93%9C

https://coding-factory.tistory.com/529#google_vignette

</aside>

<aside>
💡

[유한상태머신](https://github.com/java-squid/effective-java/issues/6) : 주어지는 모든 시간에서 처해 있을 수 있는 유한개의 상태를 가지고 주어지는 입력에 따라 어떤 상태에서 다른 상태로 전환하거나 출력이나 액션이 일어나게 하는 장치 또는 그런 장치를 나타낸 모델

</aside>

## 오토 박싱 사용시 주의

오토 박싱은 기본 타입과 박싱(Wrapper)된 기본 타입을 자동으로 변환해주는 기술

```java
void autoBoxing_Test() {
    Long sum = 0L;
    for(long i = 0; i <= Integer.MAX_VALUE; i++) {
        sum += i;
    }
}

void noneBoxing_Test() {
    long sum = 0L;
    for(long i = 0; i < Integer.MAX_VALUE; i++) {
        sum += i;
    }
}
```

sum의 변수 타입이 Wrapper 클래스 Long일때와 기본형 long일 때 속도 차이가 존재한다.

→ 박싱 타입이 필요한 경우가 아니라면 **기본 타입을 사용**하고, 의도치 않은 오토 박싱이 숨어들지 않게 주의하자.

https://sharonprogress.tistory.com/224

## 핵심 정리

이 아이템이 의미하는 바는 **객체 생성이 비싸니 무조건 피해야한다는 것이 아니다.**  프로그램의 명확성, 간결성, 기능을 위해서는 **추가 객체를 만들 수 있다.** 이 균형을 유지하는 것은 프로그래머의 몫이다.

객체 생성을 효율적으로 해보겠다고 **사소한 것들도 다 캐싱하거나 자체 풀(pool)을 만들어서** 유지보수하기 어려운 복잡한 프로그램을 만드는 것도 피해야 하는 부분이다 

→ JVM에게 위임할 부분은 위임해라 가비지 컬렉터를 신뢰하자

[Item50]방어적 복사(defensive copy) 와 대비되는 내용이기 때문에 객체 생성을 해야하는 경우와 하지 않고 기존 것을 재사용해야 하는 부분은 개발자의 역량에 달려있다 (혹은 성능 테스트를 직접 해서 비교해보아라)