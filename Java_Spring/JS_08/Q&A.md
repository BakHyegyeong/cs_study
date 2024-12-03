# JVM 힙과 스택 메모리의 역할

## Q1. 힙 메모리에서 Young Generation과 Old Generation의 역할은 무엇인가요?
- **Young Generation**: 새롭게 생성된 객체가 저장되는 영역입니다. 대부분의 객체는 짧은 수명을 가지고 GC에 의해 빠르게 수거됩니다.
- **Old Generation**: Young Generation에서 오래 살아남은 객체가 이동하는 영역으로, 더 긴 수명을 가진 객체가 저장됩니다.

## Q2. "Stop the World"란 무엇인가요?
- GC가 실행되기 위해 JVM이 애플리케이션 스레드를 일시적으로 멈추는 현상입니다. 
- GC가 메모리를 정리하는 동안 모든 애플리케이션 작업이 중단되며, 성능 저하의 주요 원인이 될 수 있습니다.

## Q3. Stop the World를 최소화하기 위해 어떤 GC 알고리즘이 사용되나요?
- **ZGC**와 **Shenandoah GC**: 두 알고리즘은 초저지연 특성을 제공하며 대부분의 GC 작업을 동시 실행합니다.

## Q4. JVM 메모리 영역(런타임 데이터 영역) 및 힙과 스택에 대해 설명해주세요
1. **힙**: 객체 생성에 따라 Young/Old Generation으로 나뉘며, GC에 의해 관리됩니다.
2. **스택**: 메서드 호출 시 생성되는 지역 변수와 연산 중간 결과를 저장하는 영역입니다.
3. **PC 레지스터**
4. **네이티브 메서드 스택**
5. **메서드 영역**

## Q5. OutOfMemoryError 발생 원인
1. **메모리 누수 (Memory Leak)**: 사용하지 않는 메모리를 해제하지 않아 발생. 컬렉션에서 자주 발생하며, 데이터 추가만 하면 이 에러가 발생할 수 있습니다.
2. **힙 메모리 과도 사용**: 너무 많은 객체를 생성하여 발생. JVM 옵션 중 힙 메모리를 늘려서 해결할 수 있습니다.

## Q6. JVM의 런타임 메모리 구조의 구성요소와 프로세스의 메모리 구조의 구성요소, 운영체제의 메모리 계층 구조를 각각 말하시오
- **JVM**: 메서드 영역, 힙 영역, 스택 영역, PC 레지스터, 네이티브 메서드 스택
- **프로세스**: 스택, 힙, 데이터, 코드
- **운영체제**: 레지스터, 캐시, 메모리, 저장장치

\+) **JVM 구성 요소**: 클래스 로더, 실행 엔진, 런타임 데이터 영역

## Q7. OutOfMemoryError 유형을 아는대로 말씀하시오
- `Java.lang.OutOfMemoryError: Java heap space`: Java의 Heap Memory 공간이 부족하여 발생
- `Java.lang.OutOfMemoryError: PermGen space`: JVM 기동 시 로딩되는 Class 또는 String의 수가 너무 많을 경우 발생
- `Java.lang.OutOfMemoryError: Requested array size exceeds VM limit`: 사용할 배열의 사이즈가 VM에서 정의된 사이즈를 초과할 때 발생
- `Java.lang.OutOfMemoryError: request bytes for. Out of swap space?`: Java는 런타임 시 물리적 메모리를 초과한 경우 가상 메모리를 확장해 사용하는데 가용한 가상 메모리가 없을 때 발생
- `Java.lang.OutOfMemoryError: (Native method)`: JVM에 설정된 것보다 큰 native 메모리가 호출될 때

\+) GC를 수행했음에도 불구하고 충분한 메모리를 할당받지 못했을 경우에도 발생

## Q8. OutOfMemoryError의 원인을 분석하는 방법에 무엇이 있는지 말씀하시오
- **Dump 파일 분석** : 애플리케이션 운영 중 장애 혹은 성능 상 문제가 발생했을 때 Dump 파일을 분석해 애플리케이션이 어떤 상태였는지, 무엇이 원인이였는지 확인
- **VisualVM**: JVM 메모리 모니터링 도구
###### Dump 파일 : 애플리케이션의 상태를 스냅샷 형태로 파일로 저장한 것

## Q9. Stop the world동안 GC는 무엇을 수행하는가?
- GC가 실행되는 동안에는 모든 객체의 참조 관계를 추적하고, 유효한 객체들과 그렇지 않은 객체들을 식별하여 메모리를 회수한다.

## Q10. Stop the world 외에 GC의 동작방식은?
- Mark and Sweep, compact 등
