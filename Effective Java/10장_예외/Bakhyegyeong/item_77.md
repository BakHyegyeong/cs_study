# 아이템 77 : 예외를 무시하지 말라

API 설계자가 메서드 선언에 예외를 명시하는 이유는 적절한 조치가 필요하기 때문이다.

→ 해당 메서드 호출을 try문으로 감싼 후 catch 블록에서 아무 일도 하지 않는 방식으로 예외를 무시하기 쉽다.

```java
try {
    // ...
} catch (SomeException e) {
    // Empty
}
```

## catch 블록

**예외는 문제 상황에 대처하기 위해 존재**하는데 catch 블록을 비워두면 예외가 존재할 이유가 없어진다.

→ 예외를 적절히 처리하면 오류를 완전히 피할 수도 있고, 무시하지 않고 바깥으로 전파되게만 둬도 최소한 디버깅 정보를 남긴 채 프로그램이 신속히 중단되게 할 수 있다.

### 예외를 무시해야 하는 경우

FileInputStream을 닫을 때와 같은 경우를 제외하고 예외를 무시할 경우

- 예외를 무시하기로 결정한 이유를 주석으로 남겨야 한다.
- 예외 변수 이름을 `ignored`로 바꿔 놓아야 한다.

```java
Future<Integer> f = exec.submit(planaMap::chromaticNumber);
int numColors = 4;

try{
   numColors = f.get(1L, TimeUnit.SECONDS);
}catch(TimeoutException | ExceutionException ignored){
   //기본값을 사용한다.(색상 수를 최소화하면 좋지만, 필수는 아님)
}
```

## 핵심 정리

예외를 던지고 catch를 하지 않는 것은 가장 피해야할 작업이며 최소한 `System.err.println()`으로 어떤 오류인지라도 파악할 수 있도록 만들자.