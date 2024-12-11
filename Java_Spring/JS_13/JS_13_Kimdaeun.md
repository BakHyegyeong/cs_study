# 스프링의 빈(bean) 생명주기

일반적인 싱글톤 타입의 스프링 빈의 생명주기
**스프링 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 → 사용 → 소멸 전 콜백 → 스프링 종료**
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeN3LIs%2FbtsI7T40sNc%2FFbg8UzOzkoLnz42JUZ9MG0%2Fimg.png">
### **1. 빈의 생명주기 단계**
1. **컨테이너 초기화**
   - 스프링 컨테이너가 설정 파일(`@Configuration`, XML 등)을 읽어 빈 정의를 확인
2. **빈 인스턴스 생성**
   - 빈 클래스의 객체를 생성 (new 키워드를 사용하여 인스턴스화)
3. **의존성 주입 :: DI**
   - 빈 정의에 따라 의존성 주입
4. **초기화 단계**
   - `@PostConstruct` 또는 `InitializingBean`의 `afterPropertiesSet()` 메서드, 혹은 `init-method`를 사용하여 초기화 작업을 실행
5. **빈 사용**
   - 애플리케이션에서 빈을 사용
6. **소멸 단계**
   - 애플리케이션 컨텍스트가 종료되면 `@PreDestroy` 또는 `DisposableBean`의 `destroy()` 메서드, 혹은 `destroy-method`를 호출하여 리소스를 정리

### **2. Bean 생명주기와 콜백**
스프링은 생명주기의 특정 시점에 작업을 실행하기 위해 다양한 **콜백 메서드**를 제공한다.

1. **`@PostConstruct` & `@PreDestroy`**
- 초기화 및 소멸 메서드로 자주 사용
   ```java
   @Component
   public class ExampleBean {
       @PostConstruct
       public void init() {
           System.out.println("Bean 초기화 중...");
       }

       @PreDestroy
       public void destroy() {
           System.out.println("Bean 소멸 중...");
       }
   }
   ```
2.	**InitializingBean & DisposableBean**
  - 인터페이스를 구현해 초기화와 소멸 작업을 정의
  ```java
  public class ExampleBean implements InitializingBean, DisposableBean {
      @Override
      public void afterPropertiesSet() {
          System.out.println("초기화 작업");
      }
  
      @Override
      public void destroy() {
          System.out.println("소멸 작업");
      }
  }
```

3.	**init-method & destroy-method**
  - XML 설정 또는 @Bean 어노테이션에서 지정 가능
  ```java
  @Bean(initMethod = "customInit", destroyMethod = "customDestroy")
  public ExampleBean exampleBean() {
      return new ExampleBean();
  }
  ``` 
---
# AOP(Aspect-Oriented Programming)이란?
AOP는 관점 지향 프로그래밍으로, 애플리케이션의 핵심 로직(비즈니스 로직)과 **부가적인 기능(공통 관심사)**을 분리하여 코드의 재사용성과 유지보수성을 높이는 프로그래밍 패러다임

### 1. AOP 주요 개념
1. Aspect (관점)
- 공통된 기능을 모듈화한 단위
- 예: 로깅, 보안, 트랜잭션 관리
2. Join Point (결합 지점)
-	Aspect가 적용될 수 있는 모든 지점
-	예: 메서드 호출, 예외 처리 등
3. Advice (충고)
-	Join Point에서 실행되는 실제 동작
-	유형: @Before, @After, @Around, @AfterReturning, @AfterThrowing.
4. Pointcut (지점 정의)
-	Join Point 중에서 특정 조건에 부합하는 지점을 정의
-	예: 특정 패키지의 모든 메서드에 적용
5. Weaving (삽입)
-	Pointcut과 Advice를 기반으로 실행 시점에 Aspect를 코드에 적용하는 과정

### 2. AOP의 장점
1. 중복 코드 제거: 공통 기능을 분리하여 코드 중복 감소
2. 유지보수성 향상: 비즈니스 로직과 부가 로직을 분리해 관리가 용이
3. 모듈화: 공통 관심사를 별도의 모듈로 분리

### 3. AOP의 한계
1. 코드 흐름이 명확하지 않아 디버깅이 어렵다.
2. 실행 성능 저하: Weaving 과정이 추가되어 성능이 약간 감소할 수 있다.
