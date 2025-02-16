# 아이템 72 : 표준 예외를 사용하라

# 표준 예외

예외를 사용할때는, 이미 자바 라이브러리에서 제공하고 있는 예외들, 즉 표준 예외를 사용하는 것이 좋다.

- 표준이기 때문에 다른 개발자가 내 코드에서 예외의 의미를 이해하기 쉬워진다.
- 가독성이 향상되어 프로그램이 읽기 쉬워진다.
- 클래스 수가 적으면 메모리 사용량도 줄고 클래스 적재 시간도 적게 걸린다.

## 자주 사용되는 예외

- `IllegalArgumentException`: 호출자가 인수로 부적절한 값을 넘길 때
    
    → 반복 횟수를 지정하는 매개변수에 음수를 건넬 경우
    
- `IllegalStateException`: 대상 객체의 상태가 호출된 메서드를 수행하기에 적절하지 않을 때
    
    → 초기화되지 않은 객체
    
- `NullPointerException`: null 값을 허용하지 않는 메서드에 null을 건낼 때
- `IndexOutOfBoundsException`: 시퀀스의 허용 범위를 넘을 때
- `ConcurrentModificationException`: 단일 스레드에서 사용하려고 설계한 객체를 여러 스레드가 동시에 수정하려고 할 때
- `UnsupportedOperationException`: 클라이언트가 요청한 동작을 대상 객체가 지원하지 않을 때

### **IllegalArgumentException VS IllegalStateException**

- `IllegalStateExceptpion` : 인수 값이 잘못 들어왔는데, 무엇이었든 어차피 실패했을 경우
- `IllegalArguementException` : 인수 값이 정상으로 들어왔을때 성공했을 경우

## 재사용하면 안되는 예외

`Exception`, `RuntimeException`,`Throwable`, `Error` 는 재사용하지 말아야 한다.

→ **다른 예외들의 상위 클래스**이므로 안정적으로 테스트할 수 없기 때문이다.

→ **여러 성격의 예외들을 포괄**하는 클래스이기 때문이다.

## 커스텀 예외의 장점

1. 예외 이름 자체가 정보를 전달할 수 있다.
2. 더 상세하게 예외 정보를 제공할 수 있다.
3. 예외의 응집도 향상 : 예외에 필요한 메시지, 전달할 정보의 데이터 등등을 한 곳에서 관리가 가능하다.
4. 정확한 오류 위치 파악 가능 : 커스텀화를 시켰기 때문에 어떤 상황에서 발생했는지 특정할 수 있다.
5. 예외 생성 비용 절감