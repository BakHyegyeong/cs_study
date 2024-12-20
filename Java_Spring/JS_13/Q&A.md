### Q1. 스프링 빈 생명주기 플로우

1. **스프링 컨테이너 생성**
2. **빈 생성**
3. **의존성 주입**
4. **초기화 콜백**
5. **사용**
6. **소멸 전 콜백**
7. **스프링 종료**

---

### Q2. 스프링 컨테이너가 관리할 빈 객체 등록 방법과 차이점

| **등록 방법**       | **설명**                                                                                 | **용도**                               |
|---------------------|-----------------------------------------------------------------------------------------|----------------------------------------|
| **`@Component`**   | 스프링이 자동으로 객체를 생성하고 빈으로 등록. 해당 클래스 자체를 빈으로 등록.              | 컨트롤러, 서비스 등 전체 클래스 등록    |
| **`@Bean`**        | `@Configuration` 클래스 내에서 명시적으로 선언하여 단일 빈 등록.                          | 특정 객체를 명확히 정의하여 등록        |

---

### Q3. 빈 생명주기 콜백

1. **인터페이스 구현**
    - 특정 인터페이스를 구현하여 콜백을 수행.
    - **장점**: 간단한 방식.
    - **단점**: 메서드명을 바꿀 수 없고, 스프링에 의존.

2. **`@Bean` 속성 지정**
    - 초기화 및 소멸 메서드를 직접 지정.
    - **장점**: 메서드명이 자유로움.
    - **단점**: 번거롭고, 스프링에 의존하지 않음.

3. **`@PostConstruct` / `@PreDestroy`**
    - Java 표준 어노테이션으로 초기화 및 소멸 콜백 처리.
    - **장점**: 최근 스프링 권장 방식, 스프링 비종속.

---

### Q4. AOP란?

- **관점 지향 프로그래밍 (Aspect-Oriented Programming)**:
    - 핵심 로직과 부가 기능을 분리하여 모듈화하는 프로그래밍 기법.
    - **장점**:
        - 로직 분산 방지.
        - 코드 재사용성 증가.

---

### Q5. 프록시 패턴이란?

- 대상 객체를 감싸는 프록시 객체를 생성해 추가적인 기능을 제공.
- **특징**:
    - 클라이언트와 서버 간 한 단계 추가.
    - **예**: Nginx가 프록시 서버로 트래픽 분산 관리.

---

### Q6. 객체의 생성과 초기화를 분리하는 이유

- **생성자**:
    - 객체 생성과 메모리 할당 담당.
- **초기화**:
    - 생성된 객체의 복잡한 설정을 처리.
- **분리 이유**:
    - 유지보수성과 코드 가독성이 향상됨.

---

### Q7. 콜백 메서드 사용 예시

- **DB 연결, 네트워크 소켓 연결 등**:
    - **시작 시점**: 연결 초기화.
    - **종료 시점**: 연결 종료.

- **빈 생명주기**:
    - 초기화 전, 소멸 전 실행될 로직 정의.

---

### Q8. AOP와 OOP의 관계

| **특징**            | **OOP**                                                     | **AOP**                                      |
|---------------------|------------------------------------------------------------|---------------------------------------------|
| **프로그래밍 방식**  | 비즈니스 로직을 객체로 모듈화                                | 핵심 로직과 공통 기능을 관점으로 모듈화      |
| **목적**            | 코드 중복 제거, 객체 재사용                                  | 핵심 로직과 공통 로직 분리, 유지보수성 향상 |
| **상호보완 관계**   | AOP는 OOP의 부족한 부분을 보완하며, 서로를 강화함            |

---

### Q9. Advice의 종류

| **타입**            | **설명**                                                                                  |
|---------------------|------------------------------------------------------------------------------------------|
| **Before**          | 조인포인트 실행 이전 실행. 일반적으로 리턴타입은 `void`.                                     |
| **After returning** | 조인포인트 완료 후 실행 (예외 없이 메서드 정상 실행 시).                                      |
| **After Throwing**  | 메서드가 예외를 던질 경우 실행.                                                             |
| **After (finally)** | 조인포인트 동작과 관계없이 항상 실행.                                                       |
| **Around**          | 메서드 호출 전후 모두 실행. 조인포인트 실행 여부, 반환 값/예외 변환, try-catch 처리 가능. 가장 강력. |
