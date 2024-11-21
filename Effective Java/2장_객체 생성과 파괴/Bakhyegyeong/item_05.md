# 아이템 5 : 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

## 많은 클래스가 하나 이상의 자원에 의존한다.

: 클래스 내에서 메서드가 사용하는 자원이 고정되어 있는 것을 의미한다.

이와 같은 방법은 **유연하지 않고 테스트하기 어렵다는 문제점**이 있다.

### 정적 유틸리티 클래스

```java
public class SpellChecker {
    private static final Lexicon DICTIONARY = null; // 편의상 null로 초기화했다.

    private SpellChecker() {

    } // 객체 생성 방지

    public static boolean isValid(String word) {
        // someThing
        return true; // 편의상 이렇게 작성했다.
    }

    public static List<String> suggestions(String typo) {
        // someThing
        return Collections.emptyList(); // 편의상 이렇게 작성했다.
    }
}
```

### 싱글턴

```java
public class SpellChecker {
    private final Lexicon dictionary = null;

    private SpellChecker() {

    }

    public static SpellChecker INSTANCE = new SpellChecker();

    public boolean isValid(String word) {
        return true; // 편의상 이렇게 작성했다.
    }

    public List<String> suggestions(String typo) {
        return Collections.emptyList(); // 편의상 이렇게 작성했다.
    }
}
```

위의 두 방식 모두 dictionary 변수 하나에 의존한다는 문제점이 있다.

→ 사용자가 다른 사전 자원을 사용할 경우 이에 대응할 수 없다.

→ final을 제거하고 **다른 사전으로 교체하는 메서드**를 추가하는 방법은 오류를 내기 쉬우며 멀티 스레드 환경에서 사용할 수 없다.

**→ 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.**

## 의존 객체 주입

: 인스턴스를 생성할 때 **생성자에 필요한 자원을 넘겨주는 방식**

→ 클래스가 여러 자원 인스턴스를 지원하고 클라이언트가 원하는 자원을 사용할 수 있다.

```java
public class SpellChecker {
    private final Lexicon dictionary;

    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
        // 자원을 넘겨받는다.
    }

    public boolean isValid(String word) {
        return true; // 편의상 이렇게 작성했다.
    }

    public List<String> suggestions(String typo) {
        return Collections.emptyList(); // 편의상 이렇게 작성했다.
    }
}
```

### 장점

1. 자원이 몇 개든 의존 관계가 어떻든 상관없이 잘 작동한다.
2. 해당 객체에 유연성을 부여하고 테스트 용이성을 개선해준다.
3. 불변을 보장하여 (같은 자원을 사용하려는) 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있기도 하다.

<aside>
💡

**의존성 주입(Dependency Injection)**

: 의존 객체 주입 방식을 활용한 디자인 패턴

외부에서 두 객체간의 관계를 결정해주는 디자인 패턴으로
인터페이스를 사이에 두어 클래스 레벨에서 의존 관계가 고정되지 않도록 도와준다.

→ **객체의 유연성**을 늘려주고 **객체간의 결합도를 낮출 수 있는 효과**

</aside>

### 팩토리 메서드 패턴 활용

: 객체를 생성할 때 어떤 클래스의 인스턴스를 만들지 서브 클래스에서 결정하게 한다.

- 추상 클래스로 UserFactory 정의

```java
public abstract class UserFactory {

    public User newInstance() {
        User user = createUser();
        user.signup();
        return user;
    }

    protected abstract User createUser();
}
```

- UserFactory를 상속받는 NaverUserFactory 정의

```java
public class NaverUserFactory extends UserFactory {
    @Override
    protected User createUser() {
        return new NaverUser();
    }
}
```

- 사용자는 NaverUser클래스에 대한 의존성 없이 User객체를 사용할 수 있다.

```java
UserFactory userFactory = new NaverUserFactory();
User user = userFactory.newInstance();
```

의존성 주입에서는 생성자에 자원 팩터리를 넘겨주는 방식으로도 활용할 수 있다.

https://bcp0109.tistory.com/367

## 핵심 정리

클래스가 **내부적으로 하나 이상의 자원에 의존**하고, 그 자원이 **클래스 동작에 영**향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 

이 자원들을 클래스가 직접 만들게 해서도 안 된다. 

대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자.

의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.