# 스프링의 빈 생명주기


> ⭐
스프링 IoC 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → **초기화 콜백 메서드 호출** → 사용 → **소멸 전 콜백 메서드 호출** → 스프링 종료


스프링 빈은 **의존관계 주입이 다 끝난 다음에야** 필요한 데이터를 사용할 수 있는 준비가 완료된다.

## Spring IoC 컨테이너 생성

![](https://img1.daumcdn.net/thumb/R960x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcwgWa4%2FbtryPrbPuYV%2F91fP1CVVaa91LXbkYy0Q40%2Fimg.png)

스프링 빈을 등록하는 방법에는 **Component-Scan과 직접 Bean을 등록하는 방법**이 있다.

위 그림에서는 Component-Scan으로 Bean 등록을 시작한다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdE7vZf%2FbtryI58l06O%2FeQPEQsHITv45v1HSpFKad0%2Fimg.png)

스프링 IoC 컨테이너 내부에 Bean으로 등록된다.

### Component-Scan

클래스 선언부 위에 `@Component` 어노테이션을 사용하는 방법

```java
@Controller
public class Controller{ 

@Service
public class Service {

@Repository 
public class Repository implements Repository {
```

**`@Controller, @Service, @Repository`는 모두 `@Component`를 포함**하고 있고, 해당 어노테이션으로 등록된 클래스들은 스프링 컨테이너에 의해 자동으로 생성되어 스프링 빈으로 등록된다.

### 자바 코드로 직접 스프링 빈 등록

자바 설정 클래스를 만들어서 직접 빈을 등록하는 방법이다.

```java
@Configuration
public class SpringConfig {

	@Bean
	public Service service() { return new Service(); }
	
	@Bean
	public Repository repository() { return new Repository(); }
```

설정 클래스를 만들고  **`@Configuration` 어노테이션을 클래스 선언부 위에 추가**한다.

특정 타입을 리턴하는 메소드를 만들고, **`@Bean` 어노테이션을 추가하면 자동으로 해당 타입의 빈 객체를 생성**된다.

## 스프링 빈 생성

![](https://img1.daumcdn.net/thumb/R960x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdrAJXL%2FbtryJvS3tLJ%2FBmTalyX9Kdq0wOqWUTIDsK%2Fimg.png)

**등록된 빈 정보를 바탕으로** **스프링 빈을 생성한다.**

## 의존성 주입

- 생성자 주입 : **객체의 생성과 의존관계 주입이 동시에** 일어난다.
- Setter, Field 주입 : **객체의 생성 → 의존관계 주입**으로 라이프 사이클이 나누어져 있다.

IoC 컨테이너에서 의존성을 주입한다.

### 객체의 생성과 초기화를 분리해야하는 이유

생성자는 파라미터를 받고 메모리를 할당하여 객체를 생성하는 역할이다. 

반면 초기화는 이렇게 생성된 객체의 값을 활용하여 무거운 동작을 수행한다. 

따라서 생성자 안에서 초기화 작업을 함께 진행하는 것 보다는 초기화 부분을 나누어 관리하는 것이 **유지보수 관점에서 좋다.**

## 빈 초기화 콜백 3가지

Bean이 생성되고, Bean의 **의존성 주입이 완료된 뒤 호출**된다.

### 콜백 메서드

특정 이벤트가 발생했을 때 호출되는 메서드

ex) DB연결, 네트워크 소켓 연결 등에서 시작 시점에 미리 연결한 뒤 **어플리케이션 종료 시점에 연결을 종료하기 위해 객체의 초기화 및 종료작업을 하는 경우**에 사용된다.

### 인터페이스 ( InitializingBean, DisposableBean )

```java
// InitializingBean 인터페이스 
@Component
class MySpringBean implements InitializingBean {

  @Override
  public void afterPropertiesSet() {
    //...
  }
}

// DisposableBean 인터페이스
@Component
class MySpringBean implements DisposableBean {

  @Override
  public void destroy() {
    //...
  }
}
```

- InitializingBean 인터페이스 : afterPropertiesSet() 메소드로 초기화를 지원 <br>
→ 의존 관계 주입이 끝난 후 호출
- DisposableBean 인터페이스 : destory() 메소드로 소멸을 지원 <br>
→ Bean 종료 전에 호출

**단점** 

- 스프링 전용 인터페이스라 코드가 **스프링에 의존**하게 된다.
- 초기화, 소멸 메소드를 오버라이드 하기 때문에 **메서드의 이름을 변경할 수가 없다.**
- 외부 라이브러리에 적용이 불가능하다.

### **@Bean 어노테이션의 어트리뷰트**

`@Bean(initMethod = "init", destroyMethod = "close")`와 같이 설정 정보에 메서드를 지정한다.

```java
public class ExampleBean {     
		public void initialize() throws Exception {        
				// 초기화 콜백 (의존관계 주입이 끝나면 호출)    
		}     
		
		public void close() throws Exception {        
				// 소멸 전 콜백 (메모리 반납, 연결 종료와 같은 과정)    
		}
		
} 

@Configuration
class LifeCycleConfig {    
 
		// Bean 어노테이션의 어트리뷰트를 통해 지정
		@Bean(initMethod = "initialize", destroyMethod = "close")    
		public ExampleBean exampleBean() {      
				// 생략    
		}
}
```

**장점**

- 메소드명을 자유롭게 부여 가능하다.
- 스프링 코드에 의존하지 않는다.
- 설정 정보를 사용하기 때문에 코드를 커스터마이징 할 수 없는 외부라이브러리에서도 적용 가능하다.

**단점**

Bean 지정시 initMethod와 destoryMethod를 직접 지정해야 하기에 번거롭다.

### **@PostConstruct, @PreDestroy 어노테이션**

```java
@Component
class MySpringBean {

  @PostConstruct
  public void postConstruct() {
    //초기화 콜백
  }

  @PreDestroy
  public void preDestroy() {
    //소멸 전 콜백
  }
}
```

**장점**

- **최신 스프링에서 가장 권장하는 방법이다.**
- 어노테이션 하나만 붙이면 되므로 매우 편리하다.
- 스프링에 종속적인 기술이 아닌 JSR-250이라는 자바 표준에 속한 어노테이션으로 스프링 의존적이지 않게 된다.

**단점**

커스터마이징이 불가능한 **외부 라이브러리에서 적용이 불가능**하다.

→ 외부 라이브러리에서 초기화, 종료를 해야 할 경우 @Bean의 어트리뷰트 기능을 사용해야 한다.

---

# Spring AOP

**관점(Aspect)지향 프로그래밍**으로, 관점을 기준으로 다양한 기능을 분리하여 보는 프로그래밍이다.

→ 어떤 로직을 기준으로 **핵심적인 관점, 부가적인 관점**으로 나누어서 보고 그 관점을 기준으로 **각각 모듈화**

- 핵심적인 관점 : 우리가 적용하고자 하는 **핵심 비즈니스 로직**
- 부가적인 관점 : 핵심 로직을 **실행하기 위해서 행해지는** 데이터베이스 연결, 로깅, 파일 입출력

## AOP의 목적

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F994AA3335C1B8C9D28)

코드 상에서 다른 부분에 반복적으로 사용되는 코드(횡단 관점)를 

→ Aspect(관점)으로 모듈화

→ 핵심적인 비즈니스 로직에서 분리

→ 코드의 간결성을 높이고 변경에 유연함과 무한한 확장

→ 재사용성 증가

를 목적으로 한다.

### 횡단 관점(관심사)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F2i0cs%2FbtsgqBq0fam%2FGCios4kAk7RdKaIAPfCLeK%2Fimg.png)

Spring에서 일반적으로 사용하는 클래스(Service, Dao 등)에서 **중복되는 공통 코드 부분(commit, rollback, log처리)을 횡단 관점(관심사)라고 부르며 이 부분을 모듈화**한다.

## Spring AOP 주요 개념

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FS0SBh%2Fbtsgu7Wj6pW%2Fz0vQxOA9C7j1iU7gf7QWJk%2Fimg.png)

- **Aspect** : **횡단 관점를 모듈화 한 것**으로 주로 부가기능을 모듈화한다.
- **Target** : Aspect를 적용하는 곳 
→ 클래스, 메서드 등
- **Advice** : 실질적으로 **어떤 일을 해야할 지**에 대한, JointPoint에서 **실행되는 코드이자 부가기능을 담은 구현체**
- **JointPoint** : **Advice가 적용될 위치** 
→ 메서드 진입 지점, 생성자 호출 시점, 필드에서 값을 꺼내올 때 등 다양한 시점에 적용가능
- **PointCut** : **JointPoint의 상세한 스펙을 정의**한 것
→ A란 메서드의 진입 시점에 호출할 것과 같이 더욱 **구체적으로 Advice가 실행될 지점**을 정할 수 있음
- Weaving : **작성한 Advice(공통 코드)를 핵심 로직 코드에 삽입**하는 단계

### Advice의 종류

| Type | 설명 |
| --- | --- |
| Before | 조인포인트 실행 이전에 실행, 일반적으로 리턴타입 void |
| After returning | 조인포인트 완료후 실행 (ex. 메서드가 예외없이 실행될 때) |
| After Throwing | 메서드가 예외를 던지는 경우 실행 |
| After (finally) | 조인포인트의 동작과 관계없이 실행 |
| Around | 메서드 호출 전후에 수행(조인포인트 실행 여부 선택, 반환 값 변환, 예외 변환, try~catch~finally 구문 처리 가능 등), 가장 강력한 어드바이스이다. |

