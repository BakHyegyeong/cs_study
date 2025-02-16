# 아이템 75 : 예외의 상세 메시지에 실패 관련 정보를 담으라

예외를 잡지 못 하고 프로그램이 실패하면 그 예외의 스택 추적(stack trace) 정보를 자동으로 출력한다.

→ 스택 추적은 예외 객체의 toString 메서드를 호출해 얻는 문자열이다.

→ 프로그래머가 얻을 수 있는 **유일한 정보인 경우가 많아 toString 메서드에 실패 원인에 관한 정보를 가능한 한 많이 담아 반환해야 한다.**

## **예외 메시지**

### 매개변수와 필드 값

실패 순간을 포착하려면 발생한 예외에 관여한 **모든 매개변수**와 **필드의 값**을 실패 메시지에 담아야 한다. <br>
→ 단 **비밀번호나 암호 키 같은 보안과 관련된 정보**는 담아서는 안된다.

**예시** 

`IndexOutOfBoundsException`의 상세 메시지는 범위의 최솟값, 최댓값, 그리고 그 범위를 벗어났다는 인덱스의 값을 담아야 한다. 

### 장황한 문서 금지

- 문서와 소스코드에서 얻을 수 있는 정보는 길게 늘어놔봐야 군더더기가 될 뿐이다.
- 예외의 상세 메시지와 최종 사용자에게 보여줄 오류 메시지를 혼동해서는 안 된다.
    
    → 사용자에게는 친절한 안내 메지리를 보여줘야 하는 반면 예외 메시지는 가독성보다는 담긴 내용이 중요하다.
    

### 상세 메시지 미리 생성하기

필요한 정보를 예외 생성자에서 모두 받아서 상세 메시지까지 미리 생성해놓는다.

```java
/**
 * IndexOutOfBoundsException을 생성한다.
 *
 * @param lowerBound 인덱스의 최솟값
 * @param upperBound 인덱스의 최댓값 + 1
 * @param index 인덱스의 실젯값
 */
public IndexOutOfBoundsException(int lowerBound, int upperBound, int index) {
   // 실패를 포착하는 상세 메시지를 생성한다.
   super(String.format("최솟값: %d, 최댓값: %d, 인덱스: %d", lowerBound, upperBound, index));

   // 프로그램에서 이용할 수 있도록 실패 정보를 저장해둔다.
   this.lowerBound = lowerBound;
   this.upperBoudn = upperBound;
   this.index = index;
}
```

실패 정보는 예외 상황을 복구하는 데 유용할 수 있으므로 **접근자 메서드(lower, upperBound, index)는 비검사 예외보다는 검사 예외에서 더 빛을 발한다.**