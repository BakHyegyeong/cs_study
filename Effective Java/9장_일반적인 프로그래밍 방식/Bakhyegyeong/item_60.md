# 아이템 60 : 정확한 답이 필요하다면 float와 double은 피하라

# float과 double

이진 부동소수점 연산에 쓰여 **근사치로 계산하도록 설계**된 타입

→ 금융 관련 계산같은 **정확한 결과가 필요할 때 부적합**하다.

```java
@Test
public void floatDoubleTest1() {
    System.out.println(1.03 - 0.42);
    // 결과: 0.6100000000000001
}
```

0.61이 아닌 0.6100…1이 나오게 된다.

## 해결 방법

BigDecimal, int, long 과 같은 정수 타입을 사용한다.

- BigDecimal
    - 여덟 가지 반올림 모드를 제공하기 때문에 반올림을 완벽히 제어할 수 있다.
    - 기본 타입보다 속도가 느리고 쓰기 불편하다.
    
    ```java
    @Test
    public void bigDecimalTest() {
        BigDecimal bigDecimal1 = new BigDecimal("1.03");
        BigDecimal bigDecimal2 = new BigDecimal("0.42");
        System.out.println(bigDecimal1.subtract(bigDecimal2));
        // 결과: 0.61
    }
    ```
    

- int, long
    - 기본 타입이므로 성능은 좋다.
    - 다룰 수 있는 값의 크기가 제한되고 소수점을 직접 관리해야 한다.

## 핵심 정리

- 정확한 답이 필요한 계산에는 float나 double을 피하자.
- 법으로 정해진 반올림을 수행해야 하는 비즈니스 계산에서는 BigDecimal을 사용하자.
- 성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크지 않다면 int나 long을 사용하자.