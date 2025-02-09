# 아이템 63 : 문자열 연결은 느리니 주의하라

# String 계산은 느리다.

- `"a" + "b"` 와 같은 방식으로 간단히 쓸 수 있지만, 성능저하가 있다.
- String 객체는 실제로 불변이기에 연산은 효율이 좋지 않다.
    
    → n 개의 문자열을 잇는다면 `n^2` 에 비례하는 시간이 걸린다.
    

```java
@Test
public void stringPlusTest() {
    String result = "";

    for (int i = 0; i < 10000; i++) {
        for (String string : strings) {
            result += string;
        }
    }
}
```

## String 객체의 값은 변경할 수 없다.

아래의 사진처럼 String의 연산은 객체 자체의 값을 업데이트시키는 것이 아닌 **업데이트된 값을 저장한 영역을 따로 만들고 다시 변수가 그 영역을 다시 참조하는 방식**으로 이루어진다.


![](https://camo.githubusercontent.com/2da7ea5ec7dab6450de8d07ecbc23aa15f08ec3a38e543a5886616814707c2ff/68747470733a2f2f696d67312e6461756d63646e2e6e65742f7468756d622f523132383078302f3f73636f64653d6d746973746f72793226666e616d653d6874747073253341253246253246626c6f672e6b616b616f63646e2e6e6574253246646e253246636d5045677a25324662747367597a7a6e3674322532466f7237314f6e3035786531385557413767764a4b4331253246696d672e706e67)

## StringBuilder 이용하기

```java
@Test
public void stringBuilderTest() {
    StringBuilder sb = new StringBuilder();

    for (int i = 0; i < 10000; i++) {
        for (String string : strings) {
            sb.append(string);
        }
    }
}
```

## StringBuilder 객체의 값은 변경할 수 있다.

객체의 공간이 부족해지는 경우 **버퍼의 크기를 유연하게 늘릴 수 있다.**

![](https://camo.githubusercontent.com/82b8c992c65502689eb05465405e91b555557878ef466f8228ac10152e30bc75/68747470733a2f2f696d67312e6461756d63646e2e6e65742f7468756d622f523132383078302f3f73636f64653d6d746973746f72793226666e616d653d6874747073253341253246253246626c6f672e6b616b616f63646e2e6e6574253246646e25324665567669473025324662747367306d4e6b684a6f253246664d5241514d3448664263643079656e317a504b344b253246696d672e706e67)

### **StringBuilder, StringBuffer의 차이**

- 공통점 : 문자열을 연산(추가, 변경)할 때 주로 사용하는 자료형
- 차이점 : 동기화 유무
    - StringBuilder : 동기화를 지원하지 않는다.
        
        → **단일 스레드 환경**에서 사용한다.
        
    - StringBuffer : 동기화를 지원한다.
        
        → 각 메서드별로 Synchronized Keyword가 존재해 **멀티 스레드 환경에서도 사용**할 수 있다.
        

**정리**

- StringBuffer : 문자열 연산이 많고 **멀티쓰레드 환경**일 경우
- StringBuilder : 문자열 연산이 많고 **단일쓰레드이거나 동기화를 고려하지 않아도 되는 경우**

## 핵심 정리

- 많은 문자열을 연결할 때는 `+` 방식보다는 StringBuilder 를 이용하자
- 멀티 스레드 환경에서는 동기화를 지원하는 `StringBuffer` 를 사용해야 한다.
- 단일 스레드 환경에서는 `StringBuilder` 가 빠르다.