# IoC(Inversion of Control)와 DI(Dependency Injection)

## 1. IoC(Inversion of Control)란?
**제어의 역전**(Inversion of Control)은 객체 생성, 의존성 관리, 생명주기 등을 개발자가 아닌 컨테이너(Spring 등)가 대신 처리하도록 하는 설계 원칙이다.
이는 객체 간의 **결합도를 낮추고 유연성과 유지보수성을 향상**시키는 데 목적이 있다.

### **IoC의 특징**
- **제어권 전환** : 객체의 생성과 의존성 관리를 컨테이너에 위임
- **유연한 설계** : 변경과 확장이 용이하며, 테스트하기 쉬운 코드 작성 가능

### **IoC 예제**
#### 전통적인 방식
```java
public class UserService {
    private UserRepository userRepository;

    public UserService() {
        this.userRepository = new UserRepository(); // 객체 직접 생성
    }
}
```
컨테이너가 UserRepository 객체를 생성하고 UserService에 주입한다. 개발자는 객체 생성과 의존성 관리에 신경 쓰지 않아도 된다.

## 2. DI(Dependency Injection)란?
의존성 주입(Dependency Injection)은 IoC를 구현하는 구체적인 방법.
객체가 필요한 의존성을 외부에서 주입받는 방식을 말한다.

### DI의 주요 개념
- **의존성** : 객체가 동작하기 위해 필요한 다른 객체
- **주입** : 컨테이너가 의존성을 객체에 전달

**DI의 유형**
1.	생성자 주입
- 의존성을 생성자를 통해 주입.
```java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```
2.	세터 주입
- 세터 메서드를 통해 의존성을 주입
```java
public class UserService {
    private UserRepository userRepository;

    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

3.	필드 주입
- 필드에 직접 주입. (Spring의 @Autowired)
```java
public class UserService {
    @Autowired
    private UserRepository userRepository;
}
```

