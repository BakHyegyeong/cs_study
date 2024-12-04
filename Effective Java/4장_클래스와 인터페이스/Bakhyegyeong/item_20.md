# 아이템 20 : 추상 클래스보다는 인터페이스를 우선하라

## 추상 클래스와 인터페이스의 메커니즘 비교

- 공통점
    - 타입을 정의하기 위한 다중 구현 메커니즘
    - 인스턴스 메서드를 구현 형태로 제공
- 차이점
    - 추상 클래스
        - 추상 클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다.
        → 자바는 단일 상속만을 지원하기 때문에 추상 클래스 방식은 새로운 타입 정의에 제약을 가진다.
    - 인터 페이스
        - 어떤 클래스를 상속했더라도 인터페이스를 정의한 클래스는 해당 인터페이스 타입 취급 가능
- 추상클래스 - 다중 상속이 불가능, 구현체와 상위-하위 클래스 관계를 갖는다.
- 인터페이스 - 다중 상속이 가능, 구현한 클래스와 같은 타입으로 취급된다.

## 인터페이스의 장점

### 기존 클래스에 손쉽게 새로운 인터페이스를 구현해 넣을 수 있다.

- 인터페이스 구현
    
    인터페이스가 요구하는 메서드를 (아직 없다면) 추가하고, 클래스 선언에 `implement` 구문만 추가하면 된다.
    

- 추상 클래스 구현
    
    두 클래스가 같은 추상 클래스를 확장하길 원한다면 그 추상 클래스는 두 클래스의 공통 조상이어야 하기 때문이다. 
    → 이런 구조는 새로 추가된 추상 클래스의 모든 자손이 이를 상속하게 되면서 문제를 일으킨다.
    

### 인터페이스는 믹스인 정의에 안성맞춤이다.


> 💡 믹스인 : 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 **주된 기능 외에도 특정 선택적 행위를 제공한다고 선언**하는 효과를 준다. <br>
→ 객체지향언어에서 다른 클래스에서 사용할 목적으로 만들어진 클래스이다.


예를 들어, 아래 코드는 `Comparable` 과 `Serializable` 를 구현해 자신을 구현한 클래스의 인스턴스들끼리는 순서를 정할 수 있다고 선언하는 **믹스인 인터페이스**이다. 

```java

public class Rank implements Comparable<Rank>, Serializable {
    
    @Override
    public int compareTo(Rank o) {
        return 0;
    }
}

```

참고로 다중 상속이 안되기 때문에 추상 클래스로는 믹스인을 정의 불가능하다.

### 계층구조가 없는 타입 프레임워크를 만들 수 있다.

타입을 계층적으로 정의하면 수많은 개념을 구조적으로 잘 표현할 수 있지만 현실에는 계층을 구분하기 어려운 개념도 있다.

예를 들어 가수와 작곡을 동시에 하는 사람을 인터페이스로 구현할 수 있다.

```java
public interface Singer {
  AudioClip sing(Song s);
}

public interface Songwriter {
  Song compose(int chartPosition);
}

// Singer, Songwriter 인터페이스를 동시에 구현하는 믹스인 인터페이스
public interface SingerSongWriter extends Singer, SongWriter{
    AudioClip strum();
    void actSensitive();
}
```

- 추상 클래스로 구현한 경우
    
    ```java
    public abstract class Comic {
    
        void laugh();
    }
    
    public abstract class Action {
    
        void fight();
    }
    
    public abstract class Melo {
    
        void love();
    }
    
    public abstract class ActionComedy {
        
        void laugh();
        void fight();
        void slapstick();
    }
    ```
    
    클래스에는 공통 기능을 정의해놓는 타입이 없으니 가능한 `2^n`개의 조합(속성이 `n` 개일때) 전부를 클래스로 정의한 고도비만 계층 구조가 만들어질 것이다. 흔히, 이러한 현상을 **조합 폭발**이라 부른다.
    

### 래퍼 클래스 관용구와 함께 사용하면, 인퍼에시는 기능을 향상시키는 안전하고 강력한 수단이 된다.

타입을 추상 클래스로 정의해두면, 그 타입에 기능을 추가하는 방법은 상속 밖에 없어진다.

→ 상속해서 만든 클래스는 래퍼 클래스보다 활용도가 떨어지고 깨지기는 더 쉽다.

**인터페이스 메서드 중 구현 방법이 명백한 것이 있다면, 디폴드 메서드로 제공할 수 있다.**

→ 단, 상속하려는 사람을 위한 설명을 `@implSpec` 자바독 태그를 붙여 문서화 해야한다. (아이템 19)


> 💡 디폴트 메서드 제약 <br> - `equals`와 `hashCode`는 디폴트 메서드로 제공해서는 안된다.<br>- 인스턴스 필드를 가질 수 없고 `public`이 아닌 정적 멤버도 가질 수 없다. (단, `private` 정적 메서드는 예외)<br>- 직접 만들지않은 인터페이스에는 디폴트 메서드를 추가할 수 없다.


## 인터페이스와 추상 골격 클래스 - 템플릿 메서드 패턴

인터페이스와 추상 골격 클래스를 함께 제공하는 방식으로, 인터페이스와 추상 클래스의 장점을 모두 취할 수 있다.

1. 인터페이스로는 타입을 정의와 필요한 디폴트 메서드를 제공한다.
2. 골격 구현 클래스로 나머지 메서드들까지 구현한다.

이렇게 되면 단순히 골격 구현을 확장하는 것만으로 인터페이스를 구현하는데 필요한 일이 완료된다. <br>
다른 말로 **템플릿 메서드 패턴(Template Method Pattern)** 라고 하며 기본 구조는 아래와 같다.

```java
interface InterfaceA {
    void methodA();
}

abstract class AbstractClassA implements InterfaceA {
    abstract void methodB();
}

class ConcreteClassA extends AbstractClassA {
    public void methodA() {
        System.out.println("method A");
    }

    public void methodB() {
        System.out.println("method B");
    }
}
```

### 추상 골격 구현 클래스

- 골격 구현 클래스를 사용하지 않은 반복적인 코드 사용

```java

public interface Athlete { 
    
    void 근육을_키우자(); 
    void 지구력을_기르자(); 
    void 연습하자(); 
    void 루틴();
}

public class SoccerPlayer implements Athlete { 
    
    @Override 
    void 근육을_키우자() { 
        System.out.println("헬스장 레츠고"); 
    }

    @Override 
    void 지구력을_기르자() { 
        System.out.println("청계천 러닝"); 
    }

    @Override 
    void 연습하자() { 
        System.out.println("슈팅 연습"); 
    }

    @Override
    void 루틴() {
        근육을_키우자();
        지구력을_기르자();
        연습하자();
    }

}

public class BaseballPlayer implements Athlete {
    
    @Override
    void 근육을_키우자() {
        System.out.println("헬스장 레츠고");
    }

    @Override
    void 지구력을_기르자() {
        System.out.println("청계천 러닝");
    }

    @Override
    void 연습하자() {
        System.out.println("배팅 연습");
    }

    @Override
    void 루틴() {
        근육을_키우자();
        지구력을_기르자();
        연습하자();
    }
}
```

SoccerPlayer와 BasketBallPlayer 클래스가 Athlete 인터페이스를 구현하면서 중복된 메서드들이 생기게 된다.

- 골격 구현 클래스를 활용해 중복 제거

```java

public interface Athlete {

    void 근육을_키우자();

    void 지구력을_기르자();

    void 연습하자();

    void 루틴();
}

// 공통된 메서드는 추상 클래스인 WangsimniAtlete에서 정의
public abstract class WangsimniAthlete implements Athlete {
    
    @Override
    public void 근육을_키우자() {
        System.out.println("헬스장 레츠고");
    }

    @Override
    public void 지구력을_기르자() {
        System.out.println("청계천 러닝");
    }

    @Override
    public void 루틴() {
        근육을_키우자();
        지구력을_기르자();
        연습하자();
    }
}

// WangsimniAthlete를 상속 후 다른 부분만 Override
public class SoccerPlayer extends WangsimniAthlete implements Athlete {

    @Override
    public void 연습하자() {
        System.out.println("슈팅 연습");
    }
}

// WangsimniAthlete를 상속 후 다른 부분만 Override
public class BaseballPlayer extends WangsimniAthlete implements Athlete {

    @Override
    public void 연습하자() {
        System.out.println("배팅 연습");
    }
}

public class Main {
    
  public static void main(String[] args) {

      List<Athlete> athletes = new ArrayList<>();
      Athlete soccerPlayer = new SoccerPlayer();
      Athlete baseballPlayer = new BaseballPlayer();
      athletes.add(soccerPlayer);
      athletes.add(baseballPlayer);
  
      for (Athlete athlete : athletes) {
        athlete.루틴();
      }
  }
}
```

디폴트 메서드를 사용하지 않고 추상 골격 구현 클래스를 구현해 중복을 제거하였다.

관례상 인터페이스 이름이 `Interface`라면 골격 구현 클래스 이름은 `AbstractInterface`로 짓는다.

→ 예시로 컬렉션 프레임워크의 `AbstractCollections`, `AbstractSet`, `AbstractList` 등이 있다.

## 핵심 정리

일반적으로 다중 구현용 타입으로는 인터페이스가 가장 적합하다. 복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법도 고려해보자. 

골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공하여 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋다. '가능한 한'이라고 한 이유는, 인터페이스에 걸려 있는 구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문이다.

결론적으로 재사용성, 유연성, 다형성 측면에서 인터페이스를 우선시하는것이 좋다.