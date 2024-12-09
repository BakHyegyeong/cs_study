# IOC (제어의 역전)

객체의 생명 주기 및 의존성 관리같은 **객체의 제어권을 개발자가 아닌 Spring 내부의 IoC 컨테이너가 담당**하는 것

## Spring IoC Container의 역할

- **객체를 생성**하고 **의존성을 구성하고 결합**하며 **생명 주기를 관리**
- **DI(Dependency Injection)를 사용**해 어플리케이션에서 구성하는 컴포넌트들을 관리
- xml파일과 java 코드, 어노테이션, java POJO 클래스를 통해 **객체에 대한 정보**를 가져온다.
→ 이렇게 가져온 객체들을 **Bean**이라 부른다.


> ⭐ <br> - Bean : Spring IoC Container에서 관리하고 있는 **애플리케이션을 실행할 때 인스턴스화 된 객체** <br> - POJO : 객체지향적으로 구현한 자바 객체


## 동작 방식

![](https://velog.velcdn.com/images/gehwan96/post/7035cbff-eba2-445e-a07b-05d45bbfcfe8/image.png)

애플리케이션을 실행할 경우, 기본적으로 구성 메타데이터(xml)와 POJOs를 Spring IOC Container에서 읽어 **Bean을 생성**하고 해당 Bean들을 통해 시스템을 사용할 수 있도록 구성한다.

# DI (의존성 주입)

클래스 사이의 의존 관계를 **외부에서 주입시키는 것**

→ IoC는 프로그램 제어권을 역전시키는 개념이고, **DI는 해당 개념을 구현하기 위해 사용하는 디자인 패턴** 중 하나이다.

## 의존성

```java
public class A {
    private B b = new B();
}
```

클래스 A는 클래스 B를 필드로 가진다.

여기서 클래스 B 내부 환경에 필드가 추가되는 등의 변화가 생긴다면 **컴파일 에러가 발생**한다.

→ **클래스 A는 B에 의존한다.**

```java
public class A {
    private B b;
    
    public A(B b) {
        this.b = b;
    }
}
```

**클래스 A는 B에 의존하는 것은 같지만 의존 대상을 직접 생성(결정)하는 것이 아니라 외부로부터 주입받는다.**

→ **의존성을 주입받는다.**

## 의존성 주입 방법

### 생성자 주입

```java
public class A {
    private B b;
    
    // 생성자에서 의존성을 주입받는다.
    public A(B b) {
        this.b = b;
    }
}
```

### Setter 주입

```java
public class A {
    private B b;
    
    // Setter 메서드에서 의존성을 주입받는다.
    public void setB(B b) {
        this.b = b;
    }
}
```

### 인터페이스 주입

```java
public interface BInjection {
		// 어떤 의존성을 주입할 것인지 인터페이스에서 명시
    void inject(B b);
}

// 의존성을 주입받는 클래스는 인터페이스의 구현체로 만든다.
public A implements BInjection {
    private B b;

    @Override
    public void inject(B b) {
        this.b = b;
    }
}
```

## DI의 장점

- 의존성이 줄어든다. (변경에 덜 취약해진다.)
- 모의 객체를 주입할 수 있기 때문에 단위 테스트가 쉬워진다.
- 가독성이 높아진다.
- 재사용성이 높아진다.
