# 가비지 컬렉션이란
- 동적으로 할당한 메모리 중 더 이상 사용되지 않는 메모리를 해제하는 프로세스.
- GC를 실행하기 위해 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개되는 Stop the World 특징을 가지고 있다.
- 장점
  - 메모리 누수를 방지하며, 메모리를 효율적으로 사용할 수 있다.
  - 메모리를 자동으로 관리하므로, 개발자가 메모리 해제를 직접하지 않아도 된다.
- 단점
  - 언제 메모리 해제가 되는지 알 수 없으며, 가비지 컬렉션 실행시간동안 모든 스레드가 멈춰, 프로그램의 성능에 영향을 줄 수 있다. -> 멈추지 말아야할 프로그램의 메모리가 해체되어, 프로그램이 종료될 수 있다.
  - 어떤 메모리를 해제할지 결정하는 비용이 든다.
- 힙

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fc37SjY%2FbtqDWT3iMNg%2Fh5HsTIvL7NPssTppNAHhXk%2Fimg.png" width="70%">

- Young 영역
  - Eden 영역 : 새로 생성된 객체가 할당되는 영역
  - Survivor 영역 : 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역
- Old 영역
  - Young 영역에서 살아남은 객체가 일정 시간이 지나면 Old 영역으로 이동한다.
- Permanent 영역
  - 응용프로그램에서 사용되는 클래스와 함수를 설명하기 위한 메타데이터를 보관하는 영역
- 처리 방식
  - Mark and Sweep
    - Mark : 사용 중인 객체들을 마크하여, 사용되지 않는 객체를 식별하는 작업
    - Sweep : Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업
    - Old 영역에서 주로 사용되는 GC로, 중단 시간이 비교적 길다.
  - Minor GC
    - Young 영역에서의 주로 사용되는 GC로, Young 영역에서 가비지 객체만 수집하므로 중단 시간이 짧음

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FGleJP%2FbtqDWr0j7SV%2FEczLkocDNoOreFYR4MLaA0%2Fimg.png" width="70%">
  
    - 새로 생성된 객체가 Eden 영역에 할당된다.
  
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FIZ0eu%2FbtqDVWMZ4i4%2FqYkOkTAiN2ky6HbKU8kcF1%2Fimg.png" width="70%">

    - Eden 영역이 가득 차게 되면, Minor GC가 실행되면서 Eden 영역에서 사용되지 않는 객체의 메모리를 해제한다.
    - Eden 영역에서 살아남은 객체는 Survivor 영역으로 이동된다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpJxQ8%2FbtqDWrTwPg8%2F5I9GkbrnxP9cPKONksGwOk%2Fimg.png" width="70%">

    - Survivor 영역이 가득 차게 되면, 살아남은 객체를 다른 Survivor 영역으로 이동시킨다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbw8e2U%2FbtqDVdIsngg%2Fk45xoXaQZ1FspuUG7NkiTk%2Fimg.png" width="70%">
  
    - Survivor 영역에서 살아남은 객체는 Old 영역으로 이동된다.

# StringBuilder와 StringBuffer의 차이
<img src="https://velog.velcdn.com/images%2Fskyepodium%2Fpost%2F22bc853f-5b76-4824-a705-6420d8adbb13%2Fimage.png" width="70%">

- 공통점 : 문자열을 연산(추가, 변경)할 때 주로 사용하는 자료형
- 차이점 :
  - String
  
  <img src="https://velog.velcdn.com/images%2Fskyepodium%2Fpost%2Ffe9a6c49-2656-459d-a6b4-dd9fa4444b06%2Fimage.png" width="70%">
  
    - String 객체의 값은 변경되지 않고 새로운 객체를 생성한다.(불변)
    - 속도가 가장 느리므로, 문자열 연산이 많은 경우 사용하지 않는다.
###### String Constant Pool 
###### Java에서 동일한 문자열을 효율적으로 관리하기 위해 JVM이 사용하는 메모리 영역이며, 힙 메모리의 일부이다. 예를 들어 "ABC"이라는 문자열 리터럴이 프로그램 내에서 여러 번 사용되더라도, 서로 다른 메모리 위치에 저장되지 않고 String Constant Pool의 같은 메모리 주소를 참조한다. 이를 통해 메모리를 절약하고 성능을 향상할 수 있다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeVviG0%2Fbtsg0mNkhJo%2FfMRAQM4HfBcd0yen1zPK4K%2Fimg.png" width="70%">

  - StringBuffer
    - 동기화를 지원하며, 각 메서드별로 Synchronized Keyword가 존재해 멀티 스레드 환경에서도 사용할 수 있다.
    - 가변적이므로 문자열 추가 연산이 많고 멀티 스레드 환경일 경우 사용한다.
  - StringBuilder
    - 동기화를 지원하지 않으며, 단일 스레드 환경에서 사용한다.(스레드 세이프 X)
    - 동기화하지 않아, 연산 속도가 빠름
    - 문자열 추가 연산이 많고 단일 스레드 환경일 경우 사용한다.
