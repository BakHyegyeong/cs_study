# 아이템 12 : toString을 항상 재정의하라

# toString

Java의 `Object`클래스에 정의된 메서드로  **객체의 정보를 문자열로 반환할 때 사용**한다.

→ 모든 Java 객체는 `Object`를 상속받기 때문에 `toString` 메서드를 가지고 있다.

## toString의 문제점

우리가 작성한 클래스에 **적합한 문자열을 반환하는 경우는 거의 없다.**

→ 아래와 같이 단순히 **클래스_이름@16진수**로 표시한 해시코드를 반환하기 때문이다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c8afdb21-c5ae-467f-8cd8-6745a5c4ad5c/ce50dd55-8877-45e9-bec1-c01ec3f4e7e4/image.png)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FBFLi8%2FbtrVeWavYOI%2F5y4ApftmwQijG3aGQhf260%2Fimg.png)

## toString의 재정의

### **간결하면서 사람이 읽기 쉬운 형태의 유익한 정보를 담아야 한다.**

```java
PhoneNumber@adbbd -> 012-1234-5678   
Car@442           -> Car{name=sun, position=2}
```

### **객체가 가진 주요 정보 모두를 반환하는게 좋다.**

```java
class Address {
    private final String city;
    private final String gu;
    private final String dong;
    private final String detail;

    Address(String city, String gu, String dong, String detail) {
        this.city = city;
        this.gu = gu;
        this.dong = dong;
        this.detail = detail;
    }

    @Override
    public String toString() {
        return "Address{" +
                "city='" + city + '\'' +
                ", gu='" + gu + '\'' +
                ", dong='" + dong + '\'' +
                ", detail='" + detail +
                '}';
    }
}
```

### **toString을 구현할 때면 반환값의 포맷을 문서화할지 정해야 한다.**

**전화번호나 행렬** 같은 값 클래스는 문서화하길 권장한다.

포맷을 명시하면 '010-1234-5678' 와 같이 **사람이 읽기 쉽고** 이 값 그대로 **입출력에 사용**하거나, **사람이 읽을 수 있는 데이터 객체로 저장**할 수도 있다.

```java
public String toString() {
    return String.format("%s-%s-%s", areaCode, prefix, lineNumber);
}

// 포맷 적용 전,
PhoneNumber{areaCode='02', prefix='512', lineNumber='1234'}

// 포맷 적용 후,
02-512-1234
```

**포맷의 문제점**

포맷을 명시하면 읽기 편하고 좋지만, 포맷을 한번 명시하면 후에 

**포맷을 바꿨을 때 기존에 작성한 코드들을 대거 수정**해야한다는 문제점이 있다.

포맷을 명시하지 않는다면 향후 릴리스에서 **정보를 더 넣거나 포맷을 개선할 수 있는 유연성**을 얻게 된다.

### **toString이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하자.**

`getter` 과 같은 메서드를 구현해 `toString`을 구성하는 각각의 데이터를 따로따로 받을 수 있는 메서드들을 제공하자는 것이다.

https://velog.io/@cchoijjinyoung/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C12-toString%EC%9D%84-%ED%95%AD%EC%83%81-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%98%EB%9D%BC