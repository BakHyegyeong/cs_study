# 스프링의 빈(bean) 생명주기
<img src="https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff457f22a-b257-4c48-b584-257f738e65e4%2FUntitled.png&blockId=5d0d9f8b-3546-4086-ac16-2e99bad12f4d" width="70%">

스프링 컨테이너는 빈을 관리하며, 컨테이너가 초기화될 때 빈 객체를 생성하여 등록, 의존성 주입을 하고 컨테이너가 종료될 시점에 빈 객체를 소멸시킨다.
## 스프링 빈 이벤트 생명주기
`스프링컨테이너 생성` -> `스프링 빈 생성` -> `의존관계 주입` -> `초기화 콜백` -> `사용` -> `소멸전 콜백` -> `스프링 종료`

### 스프링 빈 생성
- 스프링 컨테이너가 관리할 빈 객체를 생성
- 방법
  - `@Component` 어노테이션을 사용하여 빈 객체를 생성
    - `@Controller`, `@Service`, `@Repository` 어노테이션은 `@Component` 어노테이션을 포함하고 있음
    - 해당 어노테이션으로 등록된 클래스들은 스프링 컨테이너에 의해 자동으로 생성되어 스프링 빈으로 등록됨(클래스 레벨 어노테이션)
    - `@ComponentScan` 어노테이션을 사용하여 빈 객체를 생성(**패키지 내의 모든 클래스를 스캔**)
    ```java
    @Component
    public class Component {
        public void print() {
            System.out.println("Component");
        }
    }
    ```
  - `@Bean` 어노테이션을 사용하여 빈 객체를 생성
    - `@Configuration` 클래스 내에서 단일 빈을 명시적으로 선언할 때 사용
    - 설정 클래스를 만들고 `@Configuration` 어노테이션을 클래스 선언부 위에 추가(메서드 레벨 어노테이션)
    - 특정 타입을 리턴하는 메소드를 만들고 `@Bean` 어노테이션을 추가하면 자동으로 해당 타입의 빈 객체를 생성
    ```java
    @Configuration
    public class SpringConfig {
        @Bean
        public Component component() {
            return new Component();
        }
    }
    ```
    
### 의존성 주입
- 스프링 컨테이너에서 의존성을 주입
- 방법
  - 생성자 주입(권장, 테스트 코드 작성이 용이, @MockBean 어노테이션 사용 가능)
  - setter 주입
  - 필드 주입(@Autowired 어노테이션 사용, 테스트 코드 작성이 어려움, @MockBean 어노테이션 사용 불가)

### 반 생명주기 콜백
- 초기화 콜백 : 빈 객체가 생성되고 의존성 주입이 완료된 후 호출되는 메서드
  - 소멸전 콜백 : 빈 객체가 소멸되기 전에 호출되는 메서드
    - 방법
      - 인터페이스 구현
        - `InitializingBean` 인터페이스 : `afterPropertiesSet()` 메서드로 초기화 콜백 구현
        - `DisposableBean` 인터페이스 : `destroy()` 메서드 소멸 전 콜백 구현
        ```java
        public class MySpringBean implements InitializingBean, DisposableBean {
            @Override
            public void afterPropertiesSet() {
                // 초기화 작업
            }
        
            @Override
            public void destroy() {
                // 소멸 전 작업
            }
        }
        ```
        - 특징 : 스프링 전용 인터페이스 스프링에 의존
      - 빈 등록 초기화, 소멸 메서드 지정
        - `@Bean` 어노테이션 사용
        ```java
            public class ExampleBean {
                public void init() {
                    // 초기화 작업
                }
            
                public void 삭제() {
                    // 소멸 전 작업
                }
            }
            // @Bean 어노테이션의 어트리뷰트 사용
          @Bean(initMethod = "init", destroyMethod = "삭제")
            public ExampleBean exampleBean() {
                return new ExampleBean();
            }
        ```
        - 특징 : 외부 라이브러리에 적용 불가능, 메서드 이름 변경 불가능 -> 메서드 이름 변경 시 어노테이션의 어트리뷰트 변경 필요
      - `@PostConstruct`, `@PreDestroy` 어노테이션 사용
        - `@PostConstruct` 어노테이션 : 초기화 콜백 메서드에 사용
        - `@PreDestroy` 어노테이션 : 소멸 전 콜백 메서드에 사용
        ```java
        public class ExampleBean {
            @PostConstruct
            public void init() {
                // 초기화 작업
            }
        
            @PreDestroy
            public void 삭제() {
                // 소멸 전 작업
            }
        }
        ```
        - 특징 : 스프링 코드에 의존하지 않음, 메서드명 자유롭게 부여 가능(최신 스프링에서 가장 권장하는 방법)

### 스프링 빈 스코프
- 스프링 빈의 범위를 지정
- 종류
  - 싱글톤 : 하나의 빈 객체를 생성하여 모든 요청에 대해 동일한 객체를 반환
  - 프로토타입 : 요청할 때마다 새로운 빈 객체를 생성하여 반환
  - 웹 관련(리퀘스트, 세션 등) : 웹 요청마다 빈 객체를 생성하여 반환

---
# AOP(Aspect-Oriented Programming)이란?
- **관점 지향 프로그래밍**으로, **핵심 로직과 부가적 기능을 분리**하여 모듈화하는 프로그래밍 기법(로직의 흩어짐을 방지, 코드의 재사용성을 높임)
> **핵심 로직** : 비즈니스 로직 
> 
> **부가적 기능** : 로깅, 트랜잭션 처리, 보안 등

### AOP 구현 방법
1. **XML 기반 설정**
- **XML 파일**에 **AspectJ** 구현
- **\<aop:config>** 태그를 사용하여 **AspectJ** 구현
- **\<aop:aspect>** 태그를 사용하여 **Aspect** 구현
2. **Annotation 기반 설정**
- **@Aspect** 어노테이션을 사용하여 **Aspect** 구현

- 용어
  - @Aspect : 무엇을 언제 할 것인지 정의
  - @Advice : aspect가 해야하는 작업과 시기를 정의
    - @Before : 메소드 실행 전에 동작을 수행하는 Advice
    - @After : 메서드 실행 후에 동작을 수행하는 Advice
    - @AfterReturning : 메서드가 성공적으로 반환된 후에 동작을 수행하는 Advice
    - @AfterThrowing : 메서드에서 예외가 발생한 후에 동작을 수행하는 Advice
    - @Around : 메서드 실행 전후에 동작을 수행하며, 메서드 실행을 직접 제어하는 Advice



- 특징
  - 프록시 패턴 기반 : 대상 객체를 감싸는 프록시 객체를 생성하여 부가적 기능을 제공
#### 프록시 패턴
- 대상 객체를 감싸는 프록시 객체를 생성하여 대상 객체의 메서드 호출 전후에 부가적인 기능을 제공
```java
public interface Subject {
    void request();
}

public class RealSubject implements Subject {
    @Override
    public void request() {
        System.out.println("RealSubject request");
    }
}

public class Proxy implements Subject {
    private RealSubject realSubject;

    @Override
    public void request() {
        System.out.println("Proxy request");
        if (realSubject == null) {
            realSubject = new RealSubject(); // 대상 객체 생성
        }
        System.out.println("Proxy logging before"); // 메소드를 호출하기 전에 로깅
        realSubject.request();
        System.out.println("Proxy logging after");
    }
}

public class Main {
    public static void main(String[] args) {
        Proxy proxy = new Proxy(); // 프록시 객체 생성
        proxy.request();
    }
}
```
- 장점
  - **로직의 흩어짐 방지** : 부가적 기능을 별도의 클래스로 분리하여 핵심 로직과 분리
  - **코드의 재사용성** : 부가적 기능을 별도의 클래스로 분리하여 여러 대상 객체에 적용 가능
  - **관심사의 분리** : 핵심 로직과 부가적 기능을 분리하여 코드의 가독성을 높임

- 단점
  - **프록시 객체 생성** : 프록시 객체를 생성하여 대상 객체를 감싸야 하므로 객체 생성 비용이 추가됨
  - **프록시 객체의 관리** : 프록시 객체를 생성하고 관리해야 하므로 복잡성이 증가

## 예시
- **로깅** : 메서드 실행 전후에 로깅
```java
@Aspect
@Component
public class LoggingAspect {
    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Before("execution(* com.example.demo.service.*.*(..))") // com.example.demo.service 패키지의 모든 메서드
    public void before(JoinPoint joinPoint) { // JoinPoint : 실행 중인 메서드에 대한 정보
        logger.info("메서드 실행 전 : " + joinPoint.getSignature().getName());
    }

    @After("execution(* com.example.demo.service.*.*(..))")
    public void after(JoinPoint joinPoint) {
        logger.info("메서드 실행 후 : " + joinPoint.getSignature().getName());
    }
}
```

- **트랜잭션 처리** : 메서드 실행 전후에 트랜잭션 처리
```java
@Aspect
@Component
public class TransactionAspect {
    
    @AfterReturning("execution(* com.example.demo.service.*.*(..))")
    public void commit(JoinPoint joinPoint) {
        // 정상적으로 메서드 실행 시 커밋
    }
    
    @AfterThrowing("execution(* com.example.demo.service.*.*(..))")
    public void rollback(JoinPoint joinPoint) {
        // 예외 발생 시 롤백 
    }
}
```
- **보안** : 특정 메서드에 대한 접근 제어
```java
@Aspect
@Component
public class SecurityAspect {
    
    @Before("execution(* com.example.demo.service.*.*(..))")
    public void checkAuth(JoinPoint joinPoint) {
        // 사용자 권한 확인
    }
}
```
