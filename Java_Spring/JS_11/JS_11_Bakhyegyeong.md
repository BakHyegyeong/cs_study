# MVC 패턴

Model-View-Controller의 약자로써 웹 애플리케이션 아키텍쳐 중 하나이다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F2452883B57F0B3C02B)

- `model` : 내부 비지니스 로직 구현
- `Controller` : 사용자의 요청에 맞는 데이터를 모델에 요청하고 반환받는다.
- `view` : 보여지는 역할로써 Controller가 보낸 응답을 화면에 띄운다.

## MVC 패턴의 흐름과 장점

1. 사용자(User)는 컨트롤러(Controller)를 사용하여 웹 애플리케이션을 다룰 수 있다.
2. 컨트롤러는 사용자의 요청에 맞는 데이터를 모델(Model)에 요청한다.
3. 뷰(View)는 모델이 리턴한 결과를 반영한다(Updates)

→ **사용자에게 보여지는 프레젠테이션 영역과 비즈니스 로직, 데이터 구조가 서로 완전히 분리되어 있다.**

## MVC 패턴의 분류

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F270EFE4C57F0C7A61C)

- mvc 1패턴 : 비즈니스 로직 영역(Controller)에 프레젠테이션 영역(View)을 같이 구현하는 방식
html과 java코드가 합쳐져있어 view와 controller의 기능을 각각 수행한다.
    
    → 빠른 웹 제작을 필요로 하는 소규모 프로젝트에 사용됨
    

- mvc 2패턴 : 비즈니스 로직 영역과 프레젠테이션 영역이 분리되어 있는 구현 방식
html과 java를 분리해 한 파일에서 view와 controller의 기능이 수행된다.
    
    → 대규모 프로젝트에 용이하다.
    

| 구분 | 모델1 | 모델2 |
| --- | --- | --- |
| 컨트롤러와 뷰의 분리 여부 | 통합(JSP 파일) | 분리(JSP, Servlet) |
| 장점 | 쉽고 빠른 개발 | 디자이너/개발자 분업 유리, 유지보수에 유리 |
| 단점 | 유지보수가 어려움 | 설계가 어려움, 개발 난이도가 높음 |


---

# Spring가 Spring Boot의 차이점

## Spring

**Java 기반 애플리케이션 개발을 지원하는 오픈소스 애플리케이션 프레임워크**

### Spring의 특징

1. 제어 역전 : 스프링은 **객체의 생명 주기 및 의존성 관리를 담당하는 IoC 컨테이너를 제공한다.
→** 개발자는 객체의 생성과 관계 설정을 **스프링에 위임**할 수 있으며, **스프링 컨테이너가 객체의 생명 주기를 관리하고 필요한 의존성을 주입**한다.
2. 의존성 주입 : 스프링은 의존성 주입을 통해 **객체 간의 관계를 설정**한다.
→ **애플리케이션의 결합도를 낮추고 유연성과 테스트 용이성**을 향상 시킨다.
3. AOP 지원(관점 지향 프로그래밍) : 애플리케이션의 **핵심 비즈니스 로직과 부가적인 기능(로깅, 트랜잭션 관리 등)을 분리하여 모듈화**할 수 있다.

### Spring의 문제점

1. 설정의 복잡성 : 기능을 사용하기 위해 많은 설정과 구성이 필요하다. 
    
    → 애플리케이션 컨텍스트 설정, 빈 정의, 다양한 컴포넌트 구성을 위한 설정이 있고 **이 과정이 복잡하고 초보자에게는 어렵다.**
    
2. 의존성 관리 문제 : 의존성 주입을 구현하기 위해 XML 설정 파일에 수많은 빈(Bean)을 직접 등록해야 한다.

## Spring Boot

**스프링의 문제점을 해결해 주기 위해 개발된 스프링의 프레임워크**

### Spring Boot의 특징

1. 간결한 설정 : 스프링 부트는 번거로운 XML 설정이 필요 없으며, 최소한의 설정으로 Spring을 사용할 수 있고, **기본적인 설정을 자동으로 처리하므로 개발자가 많은 설정 작업을 하지 않아도 된다.**
2. 내장 서버 : 내장된 서버(내장 Tomcat, Jetty, Undertow)를 제공하여 **별도의 서버 설정 없이 애플리케이션을 실행할 수 있다. 
→** 배포를 위해 War 파일을 생성해서 Tomcat에 배포할 필요 없으며, **JAR 파일에는 모든 의존성 라이브러리가 포함되어 있어 외부 서버 없이도 애플리케이션을 실행할 수 있다.** 
3. 의존성 관리 최소화 : 이미 테스트된 여러 라이브러리들의 묶음 패키지를 제공한다.
→ **‘starter’ 의존성 통합 모듈(`spring-boot-starter-test`)을 제공하여 Maven/Gradel 설정 시 버전 관리가 간편**하다.
4. 운영 편의성 : 애플리케이션의 상태 모니터링, 로깅, 보안 설정 등 운영에 필요한 기능들을 제공한다.

## Spring과 Spring Boot의 차이점

### 의존성 주입

- Spring : 의존성을 설정해줄 때 설정 파일이 매우 길고, 모든 의존성에 대해 버전 관리도 하나하나 해줘야한다.
- Spring Boot : Spring보다 간단하게 설정할 수 있고, 버전 관리를 자동으로 해준다.

```java
// Spring
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.3.5</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.5</version>
</dependency>

// Spring Boot
implementation 'org.springframework.boot:spring-boot-starter-web'
```

### 환경 설정

- Spring : 설정이 매우 길고, 모든 어노테이션 및 빈 등록 등을 직접 해주어야 한다.

```java
@Configuration
@EnableWebMvc
public class MvcWebConfig implements WebMvcConfigurer {

    @Autowired
    private ApplicationContext applicationContext;

    @Bean
    public SpringResourceTemplateResolver templateResolver() {
        SpringResourceTemplateResolver templateResolver = 
          new SpringResourceTemplateResolver();
        templateResolver.setApplicationContext(applicationContext);
        templateResolver.setPrefix("/WEB-INF/views/");
        templateResolver.setSuffix(".html");
        return templateResolver;
    }

    @Bean
    public SpringTemplateEngine templateEngine() {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver());
        templateEngine.setEnableSpringELCompiler(true);
        return templateEngine;
    }

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        ThymeleafViewResolver resolver = new ThymeleafViewResolver();
        resolver.setTemplateEngine(templateEngine());
        registry.viewResolver(resolver);
    }
}
```

<br>

- Spring Boot : application.properties파일이나 application.yml파일에 설정한다.

```java
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf
```

### 배포

- Spring : war파일을 Web Application Server에 담아 배포
- Spring Boot : Tomcat 이나 Jetty 같은 내장 WAS를 가지고 있기 때문에 jar 파일로 간편하게 배포

## 정리

| 구분 | 스프링(Spring) | 스프링 부트(Spring Boot) |
| --- | --- | --- |
| 정의 | 프레임워크 | 스프링 프레임워크 기반의 도구 |
| 설정 | 설정 파일 작성 필요 | 자동 설정 제공 |
| 서버 | 내장 서버 없음 | 내장 서버 제공 |
| 사용 목적 | 세밀한 제어 필요할 때 | 빠르고 간단한 애플리케이션 개발할 때 |
