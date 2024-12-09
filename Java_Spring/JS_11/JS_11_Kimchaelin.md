# MVC 패턴이란?
<img src="https://mblogthumb-phinf.pstatic.net/MjAxNzAzMjVfMTM0/MDAxNDkwNDQyNDI5OTAy.MUksll6Y9SzelJjmGW6zXOlPebJKOft3OhcnmhrcmTgg.4g4FxlhwEpgxp8kGXJVLf2LHlrRJhP7NqR7LJew8tL0g.PNG.jhc9639/ModelViewControllerDiagram.png?type=w800" width="70%">

- Model-View-Controller의 약자로, 웹 애플리케이션 아키텍쳐 중 하나이다.
###### 웹 애플리케이션 아키텍처 : 웹 애플리케이션을 구성하는 구조 및 설계
- `model` : 내부 비지니스 로직 구현
  - 데이터베이스와의 상호작용, 데이터 처리 로직 등 비즈니스 로직을 담당한다.
- `view` : 보여지는 역할로써 Controller가 보낸 응답을 화면에 띄운다.
- `Controller` : 사용자의 요청에 맞는 데이터를 모델에 요청하고 반환받는다.
  - 사용자의 요청을 받아 모델에 데이터를 요청하고, 그 결과를 뷰에 전달한다.

## MVC 패턴의 흐름
1.	사용자가 요청(입력)을 보냄
2.	Controller가 사용자의 요청 받아, Model에 데이터 요청
3.	Model은 요청에 따라 데이터를 처리하거나 상태를 변경
4.	Controller는 Model로부터 데이터를 받아 View에 전달
5.	View는 데이터를 사용자에게 보여줌

## MVC 패턴의 장점과 단점
### 장점
- 역할 분리: 각 구성 요소가 독립적으로 동작하므로 코드의 가독성과 유지보수성이 향상된다
- 재사용성: View와 Model을 분리하여 다양한 View에서 동일한 Model을 재사용할 수 있다.

### 단점
- 복잡성: 구현시 세 가지 구성 요소를 모두 고려해야 하므로 복잡성이 증가한다.

## MVC 패턴의 분류
- mvc 1패턴
  - html과 java 코드가 합쳐져있어 view와 controller의 기능을 각각 수행한다.
  - 빠른 웹 제작을 필요로 하는 소규모 프로젝트에 사용됨
- mvc 2패턴
  - html과 java를 분리해 한 파일에서 view와 controller의 기능이 수행된다.
  - 대규모 프로젝트에 용이하다.

# 스프링(Spring)과 스프링 부트(Spring Boot)의 차이

## 1. 스프링(Spring Framework)
스프링은 Java 기반의 **엔터프라이즈 애플리케이션 개발**을 위한 프레임워크입니다.

### 주요 특징
- **풍부한 기능**: DI(의존성 주입), AOP(관점 지향 프로그래밍), 트랜잭션 관리, 데이터 접근, 웹 MVC 등.
- **유연한 설정**: XML, Java Config, Annotation 기반의 설정 방식 지원.
- **모듈화 구조**: 필요한 모듈(Core, Beans, MVC 등)을 선택적으로 사용할 수 있음.

### 사용 시 주의점
- 설정이 복잡하여 초기 설정 작업이 많음.

---

## 2. 스프링 부트(Spring Boot)
스프링 부트는 스프링 기반 애플리케이션을 쉽게 개발할 수 있도록 **설정을 자동화**한 프레임워크입니다.

### 주요 특징
- **자동 설정(Auto Configuration)**: 기본 설정을 자동으로 제공하여 초기 설정 부담 감소.
- **의존성 관리**: Maven/Gradle의 Starter 제공으로 의존성 관리 간소화.
    - 예: `spring-boot-starter-web`, `spring-boot-starter-data-jpa`
- **내장 서버 제공**: Tomcat, Jetty 등의 서버가 내장되어 있어 별도 설치 필요 없음.
- **운영 환경 지원**: 프로파일 기반 설정(dev, prod 등) 및 로깅 설정 지원.

### 장점
- 빠르게 개발 환경을 구축하고 실행할 수 있음.
- 설정과 배포 과정이 간소화됨.

---

## 3. 차이점 비교

| **특징**       | **스프링(Spring)**            | **스프링 부트(Spring Boot)**                    |
|--------------|----------------------------|-----------------------------------------------|
| **설정**       | 수동 설정(XML, Java Config) 필요 | 자동 설정(Auto Configuration) 제공             |
| **의존성 관리**   | 개별 라이브러리 직접 추가             | Starter로 의존성 관리 간소화                    |
| **내장 서버**    | 없음 (별도 서버 필요)              | Tomcat 등 내장 서버 제공                        |
| **운영 환경 관리** | 직접 작성 필요                   | 프로파일 기반 환경 관리 제공                   |
| **배포**       | War 파일로 배포(jar+웹 어플리케이션 서버) | Jar 파일로 배포(내장 서버로 실행)                |
| **목적**       | 세부 설정과 커스터마이징이 필요한 경우      | 빠르게 배포 가능한 애플리케이션 개발            |

---
