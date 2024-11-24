# 아이템 10 : equals는 일반 규약을 지켜 재정의하라

# equals

: 두 객체의 **논리적 동치성**을 비교하기 위해 사용하는 메서드이다.

<aside>
💡

논리적 동치성 : 다른 클래스 인스턴스이지만 **핵심 필드를 비교했을 때 동일한 인스턴스로 판별**하는 것

</aside>

## eqauls 를 재정의하지 않을 경우

`equals`를 재정의하지 않는다면 상위 클래스의 `eqauls`를 사용하게 되고 끝까지 올라간다면 `Object`의 `equals`를 사용하게 된다.

→ **`Object`의 `equals`의 경우 단순히 자기 자신과 같은 지를 비교한다.**

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

## **equals를 재정의 하면 안 되는 경우**

1. 각 인스턴스가 본질적으로 고유할 때
    
    값 클래스(`Integer`나 `String`처럼 값을 표현하는 클래스)가 아닌 동작하는 개체를 표현하는 클래스
    
    ex) `Thread`
    
2. 인스턴스의 '논리적 통치성'을 검사할 일이 없을 때
    
    ex) `java.util.regax.Pattern`은 `equals`를 재정의해 두 `Pattern`의 정규표현식을 비교
    
3. 상위 클래스에서 재정의한 `equals`가 하위 클래스에도 딱 들어맞을 때
    
    ex) `Set`은 `AbstractSet`이 구현한 `equals`를 상속, `List`는 `AbstractList`, `Map`은 `AbstractMap`
    
4. 클래스가 *private*이나 *package-private*이고 `equals`를 호출할 일이 없을 때
    
    아래와 같이 구현해 `equals`가 실수로라도 호출되는 걸 막을 수 있다.
    
    ```java
    @Override public boolean equals(Object o) {
    	throw new AssertionError(); // 호출 금지!
    }
    ```
    

## equals를 재정의 해야 하는 경우

객체 식별성이 아닌 **논리적 동치성을 확인**해야 하는데

상위 클래스(주로 값 클래스)의 `equals`가 **논리적 동치성을 비교하도록 재정의 되지 않았을 때** 재정의 해야 한다.

<aside>
💡

객체 식별성 : 두 객체가 물리적으로 같은가

</aside>

## equals의 일반 규약

null이 아닌 모든 참조 값 x, y, z에 대해,

- 반사성
    - x.equals(x) == true
- 대칭성
    - x.equals(y) == true
        
        → y.equals(x) == true
        
- 추이성
    - x.equals(y) == true
        
        →  y.equals(z) == true
        
        → x.equals(z) == true
        
- 일관성
    - x.equals(y)를 반복해도 값이 변하지 않는다.
- null-아님
    - x.equals(null) == false

### 반사성

: 객체가 자기 자신과 같아야 한다.

### 대칭성

: 두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다.

### 추이성

: 첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같아면, 첫 번째 객체와 세 번째 객체도 같아야 한다.

추이성에서는 아래와 같은 문제가 생길 수 있다.

- 무한 재귀
- 리스코프 치환 원칙

### 일관성

: 두 객체가 같다면 (어느 하나 혹은 두 객체 모두가 수정되지 않는 한) 앞으로도 영원히 같아야 한다.

### null-아님

: 모든 객체가 `null`과 같지 않아야 한다.

## equals의 좋은 재정의 방법

```java
@Override
public boolean equals(final Object o) {
    // 1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
    if (this == o) {
        return true;
    }

    // 2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
    if (!(o instanceof Point)) {
        return false;
    }

    // 3. 입력을 올바른 타입으로 형변환 한다.
    final Piece piece = (Piece) o;

    // 4. 입력 개체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다.
    
    // float와 double을 제외한 기본 타입 필드는 ==를 사용한다.
    return this.x == p.x && this.y == p.y;
    
    // 필드가 참조 차입이라면 equals를 사용한다.
    return str1.equals(str2);
    
    // null 값을 정상 값으로 취급한다면 Objects.equals로 NullPointException을 예방하자.
    return Objects.equals(Object, Object);
}
```

## 핵심 정리

- 꼭 필요한 경우가 아니면 `equals`를 재정의하지 말자.
    
    → 많은 경우에 `Object`의 `equals`가 여러분이 원하는 비교를 정확히 수행해준다. 
    
- 재정의해야 할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯 가지 규약을 확실히 지켜가며 비교해야 한다.

https://velog.io/@lychee/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C-10.-equals%EB%8A%94-%EC%9D%BC%EB%B0%98-%EA%B7%9C%EC%95%BD%EC%9D%84-%EC%A7%80%EC%BC%9C-%EC%9E%AC%EC%A0%95%EC%9D%98-%ED%95%98%EB%9D%BC