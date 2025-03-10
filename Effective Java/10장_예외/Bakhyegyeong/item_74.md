# 아이템 74 : 메서드가 던지는 모든 예외를 문서화하라

메서드가 던지는 예외는 그 메서드를 올바로 사용하는 데 아주 중요한 정보다. 

→  발생 가능한 예외를 문서로 남기지 않으면 그 클래스나 인터페이스를 효과적으로 사용하기 어렵거나 불가능할 수도 있다.

→ 예외 하나하나를 문서화하는 데 충분한 시간을 쏟아야 한다. 

## 예외를 문서화 하는 방법

### 검사 예외

검사 예외는 항상 따로따로 선언하고, 각 예외가 발생하는 상황을 자바독의 `@throws` 태그를 사용하여 문서화한다.

- 공통 상위 클래스 하나로 문서화하는 일은 삼가야 한다.
    
    → main은 오직 JVM만이 호출하므로 Exception을 던지도록 선언해도 괜찮다. 
    

```java
/ **
 * 주석의 설명문
 * 
 * @throws java.io.FileNotFoundException 지정된 파일을 찾을 수 없습니다
 * /
```

### 비검사 예외

일반적으로 프로그래밍 오류를 뜻하는데, 프로그래머가 자연스럽게 해당 오류가 나지 않도록 코딩하게 해준다.

- 비검사 예외를 구분하기 위해 비검사 예외는 메서드 선언의 throws 목록에 넣지 말아야 한다.
- 인터페이스 메서드에서는 해당 인터페이스를 구현하는 구현체가 참고할 수 있는 좋은 수단이 된다.

### 클래스에 예외 명시

한 클래스에 정의된 많은 메서드가 같은 이유로 같은 예외를 던진다면 그 예외를 클래스 설명에 추가하는 방법도 있다.

## 핵심 정리

- 메서드가 던질 가능성이 있는 모든 예외를 문서화하라. 검사/비검사 예외, 추상/구체 메서드 모두에 해당한다.
- 문서화에는 자바독의 `@throws` 태그를 사용하고, 검사 예외만 메서드 선언의 `throws` 문에 일일히 선언한다.