## 스프링 특징 3가지
1. 제어 역전(IoC)
2. 의존성 주입(DI)
3. 관점 지향 프로그래밍(AOP)


# IoC(Inversion of Control)란?
- 제어 역전이라고 불리며, 객체의 생성, 생명주기, 의존성 관리 등을 개발자가 아닌 프레임워크나 컨테이너에 위임하는 것을 말한다.
- 객체 간의 결합도를 낮추고 유연성과 유지보수성을 향상시킴
- Ioc를 구현하는 방법 중 하나가 DI(Dependency Injection)이다.

# 의존성이란?
- 객체가 다른 객체가 필요하다는 것
- **의존성 주입** : 외부로부터 파라미터나 필드로 다른 객체를 받아 사용하는 것.(참조하는 것)
- ApplicationContext : 의존성이 필요한 객체를 찾아서 필요한 객체를 주입해주는 역할을 한다.
  - ApplicationContext가 관리하는 객체들을 **빈(bean)**이라고 부르며, 빈과 빈 사이의 의존성을 관리한다.

# 의존성 주입(DI)이란?
- 객체에게 필요한 객체를 외부에서 주입 받는 것
- 객체 간의 의존 관계를 느슨하게 하여 유연한 코드를 작성할 수 있게 해준다.
- 구현 방법
  - 생성자 주입
    ```java
    public class A {
        private B b;
        
        public A(B b) {
            this.b = b;
        }
    }
    ```
  - Setter 주입
      ```java
        public class A {
            private B b;
            
            public void setB(B b) {
                this.b = b;
            }
        }
      ```
  - 필드 주입
      ```java
          public class A {
              @Autowired // 자동으로 의존성을 주입하기 위해 사용하는 어노테이션
              private B b;
          }
      ```
