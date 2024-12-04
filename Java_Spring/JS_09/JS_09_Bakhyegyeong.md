# 객체 지향 프로그래밍

## 객체지향 프로그래밍과 절차적 프로그래밍

- 객체지향 프로그래밍 (Object Oriented Programming)
    
    : **Object(Real World영역에서의 객체)들이 모여서 상호협력하면서 데이터를 처리하는 방식**의 프로그래밍 설계 방법
    
    → **Real World(현실)에서의 어떤 Object를 추상화를 통해 Programming 언어로 표현하는 것.**
    
- 절차적 프로그래밍 (Procedure Programming) : **함수를 이용해서 정리 정돈하는 프로그래밍** 기법

![](https://velog.velcdn.com/images/calla3494qhk/post/bb6c5945-6ec6-42e6-a5b7-3285bff0cdf0/image.png)

### 객체 지향 프로그래밍의 장단점

- **장점**
    - 다른 클래스를 가져와 사용할 수 있고, 상속받을 수 있어 코드 재사용성 증가
    - 클래스 단위의 모듈화가 가능해 대형 프로젝트에 적합
    - 객체 단위로 코드가 나눠서 작성되기 때문에 디버깅이 쉽고 요지보수가 용이함

- **단점**
    - 처리 속도가 상대적으로 느림
    - 객체가 많으면 용량이 커짐
    - 설계 시 많은 노력과 시간이 필요

### 객체와 클래스의 차이

- 클래스 : 객체를 생성하기 위한 템플릿, 객체의 가능한 상태와 행동을 정의
- 객체 : 클래스에 정의된 것을 기반으로 생성된 실체로 클래스의 인스턴스이다.

### 소프트웨어의 모듈 독립성

: 모듈은 기능적으로 나뉘어져있어야 하며 모듈에 대한 응집력은 낮아야 한다.

→ 오류가 발생했을 때 다른 모듈에 끼치는 영향이 적다.

- 모듈화 : 문제를 작은 부분으로 쪼개나가는 것.
- 모듈 : 소프트웨어를 각 기능별로 나누어진 소스 단위

<br>

- **결합도** : 모듈간의 상호 의존 정도
    
    → 협력에 필요한 적절한 수준의 관계만을 유지하는 것.→ **결합도는 낮을수록 좋다.**
    
    - 자료결합도 : 모듈끼리 단순히 데이터를 주고 받는 것. (다른 로직 사용X)
- **응집도** : 하나의 클래스가 기능에 집중하기 위한 모든 정보와 역할을 갖고 있어야 한다.
    
    → a기능이 B,C 모듈에 있는 것이 아닌 A모듈안에 있는 것.→ **응집도는 높을수록 좋다.**
    
    - 기능적 응집도 : 모듈 내부의 모든 기능이 단일 목적을 위해 수행되는 것.

## 추상화

**Object가 가진 명사적특징과 동사적특징을 class로 나타내는 것**

- 데이터 추상화 : 객체의 관련 속성만 **표시**하는 것
    
    → 대상을 간단한 개념으로 일반화하는 과정
    
    ex) 아이폰 → 휴대폰 → 통신기기 → 전자제품
    
- 제어 추상화 : 불필요한 세부 정보는 **숨김**
    
    ex) 내부로직을 모른채 다른 클래스의 메서드를 호출하는 것.
    
![](https://velog.velcdn.com/images/calla3494qhk/post/89e8a8e0-c4d7-4e1f-a5fa-33392540c544/image.png)

- `Class` : **Instance(프로그래밍 영역의 객체)를 생성하기 위한 하나의 템플릿**
    
    → **Instance와는 다른 개념이다.**
    
    → **class의 변수와 함수는 Instance의 소유이다.**
    
    - 변수 : 생성된 instance의 주소값(JVM의 메모리에 저장되는 값)을 담는 것
    - 함수

## 상속  (`extends`,`implements`)

부모의 메서드를 자식이 사용할 수 있는 것

→ 중복 코드 제거, 코드 재사용성 증가

→ 모호성 때문에 다중상속(하나의 클래스가 여러 부모 클래스를 두는 것)을 허용하지 않고 단일상속만을 허용함.

→ **결합도를 상승**시킨다.

![](https://velog.velcdn.com/images/calla3494qhk/post/c9c530f8-2827-4da8-b3bf-4d5368bc3871/image.png)

- class - class 상속 : `extends`
- interface - class 상속(구현) : `implements`

## 다형성

### method의 다형성

: interface를 통해 객체는 다르지만 메서드 이름이 같은 것.

→ 객체를 바꾸더라도 메서드 이름은 같기에 크게 수정할 필요가 없음.

→ 예시로 **오버라이드와 오버라이딩**이 있다.

```java
SamsungTV tv1 = new SamsungTV();
LgTV tv2 = new LgTV();

tv1.powerOn();
tv1.volumnUp();
tv1.channelUp();

tv2.powerOn();
tv2.volumnUp();
tv2.channelUp();
```

### 객체타입의 다형성

: 부모 interface가 자식객체를 담을 수 있는 것.

```java
TV tv = new SamsungTV();
```

## 은닉화(캡슐화)

정보에 대한 유효성을 검증과 정보 은닉을 통한 데이터 보호 <br>
→ 예시로 `setter`, `getter` 이 있다.

```jsx
public class Person{
    private int age;
    private String name;
 
    public int getAge() {
        return age;
    }
 
    public String getName() {
        return name;
    }
}

```

---

# **객체지양의 설계 원칙(SOLID)**

## 단일 책임 원칙 (**S**ingle Responsibility Principle)

: 객체는 단 하나의 책임(기능)만 가져야 한다.

→ 기능 변경이 일어났을 때 영향을 끼치는 것이 적는 것.

![](https://velog.velcdn.com/images/calla3494qhk/post/80c1c3b9-857f-4a13-bd04-29bca44db0d3/image.png)

## 개방 폐쇄 원칙 (**O**pen Closed Principle)

: 기능을 추가할 때 기존의 코드를 변경하지 않도록 설계하는 것.

→ 확장에 대해서는 개방적(Open)이고, 수정에 대해서는 폐쇄적(Closed)어야한다는 것.

- 변경되어야할 것과 변경되지 않을 것을 분류한다.
- 변경되어야할 두 모듈이 만나는 지점에 추상클래스나 인터페이스를 생성한다.
- 구현체에 의존하기 보다는 정의한 추상화에 의존하도록 코드를 작성한다.

## 리스코프 치환 원칙 (**L**iskov Substitution Principle)

: **서브 타입(자식)은 언제나 기반 타입(부모)으로 교체할 수 있어야한다.**

→ 이를 위해서 override이 잘 이루어져 있어야 한다.

• 포함관계 : **부모보다 자식이 포함하는 범위가 더 넓다.
→** 부모 객체가 자식 객체를 담을 수 있다는 것이지 포함관계가 더 크다는 것은 아니다.

```java
TV tv = new SamsungTV();

tv.powerOn();
tv.volumnUp();
tv.channelUp();
```

**→ 부모타입으로 자식객체를 담았을 경우에는 자식타입의 객체가 아닌 부모타입의 객체로 저장되어 자식객체의 메서드를 사용할 수 없음.**


![](https://velog.velcdn.com/images/calla3494qhk/post/1abad5eb-fbd4-4388-82b2-5a35050189b3/image.png)

### Virtual Method Invocation

**override의 경우에는 자식 클래스부터 메서드를 찾기 때문에 부모 클래스의 기존 메서드가 아닌, 자식 클래스의 override된 메서드를 호출하게 된다.**

- `super` : 부모 class를 의미
- `this` : 현재 class를 의미

![](https://velog.velcdn.com/images/calla3494qhk/post/ec6de5ce-73cd-42d8-af64-f5d3909c830b/image.png)

## 인터페이스 분리 원칙 (**I**nterface Segregation Principle)

: interface를 소규모로 만들어서 client가 사용할 수 있게끔 분리해야한다.

→ 인터페이스를 사용에 맞게 끔 각기 분리하여야 한다.

![](https://velog.velcdn.com/images/calla3494qhk/post/b31ba4a1-44b7-4a18-b077-91e191e5ebeb/image.png)


### 목적

## 의존 역전 원칙 (**D**ependency Inversion Principle)

: 객체에서 어떤 class를 참조해서 사용해야한다면,

**그 class를 직접 참조하는 것이 아니라 그 대상의 상위요소(추상 클래스 or 인터페이스)로 참조하라는 원칙**

→ 하위 모듈에 변화가 있을 때마다 수정해야하는 코드의 양이 줄어들게 된다.
