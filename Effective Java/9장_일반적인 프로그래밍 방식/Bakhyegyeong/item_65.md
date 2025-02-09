# 아이템 65 : 리플렉션보다는 인터페이스를 사용하라

# 리플렉션

실행 중인 자바 애플리케이션이 **런타임 동작을 검사, 수정, 상호작용할 수 있도록 하는 기능**이다.

1. 클래스 구조 검사 : 객체의 클래스를 확인하고 어떤 메서드와 필드가 있는지 등의 정보를 얻는다.
2. 클래스의 인스턴스 생성 : 컴파일 시점에 클래스 이름을 몰라도 클래스의 새 인스턴스를 만들 수 있다.
3. 메서드 호출 및 필드 액세스 : 컴파일 시점에 메서드를 몰라도 동적으로 객체의 메서드를 호출하고 필드에 액세스 할 수 있다.

## 리플렉션 단점

1. 컴파일 타임 **타입 검사가 주는 이점**을 누릴 수 없다.
    
    → 리플렉션 기능을 써서 **존재하지 않는 혹은 접근할 수 없는 메서드**를 호출하려 시도하면 런타임 오류가 발생한다.
    
2. 리플렉션을 이용하면 코드가 지저분하고 장황해진다.
3. 성능이 떨어진다.
    - 리플렉션을 통한 메서드 호출은 일반 메서드 호출보다 훨씬 느리다.
    - 고려해야 하는 요소들이 많아 오버헤드가 발생할 수 있다.

## 리플렉션의 올바른 사용

- 코드 분석 도구나 의존관계 주입 프레임워크와 같은 **복잡한 애플리케이션**
- 인스턴스 생성 : 동적으로 클래스를 생성하고 접근해야 할 경우
    
    → 인스턴스 참조는 적절한 인터페이스나 상위 클래스를 이용
    

### 테스트 코드

사용자로부터 클래스 경로 문자열을 입력받고 이를 통해 해당 클래스를 가져오고 가져온 클래스를 조작하는 코드이다.

```java
// 클래스 이름을 Class 객체로 변환
Class<? extends Set<String>> cl = null;
try {
    cl = (Class<? extends Set<String>>)
                    Class.forName(args[0]); // 사용자에게 입력받음
} catch (ClassNotFoundException e) {
    // 해당 경로에서 클래스를 찾을 수 없을 때
    e.printStackTrace();
}

// 생성자를 얻는다.
Constructor<? extends Set<String>> cons = null;
try {
    cons = cl.getDeclaredConstructor();
} catch (NoSuchMethodException e) {
    // 매개변수 없는 생성자가 없을 때
    e.printStackTrace();
}

// 집합의 인스턴스를 만든다.
Set<String> s = null;
try {
    s = cons.newInstance();
} catch (IllegalAccessException e) {
    // 생성자가 예외를 던졌을 때
    e.printStackTrace();
} catch (InstantiationException e) {
    // 클래스를 인스턴스화할 수 없을 때
    e.printStackTrace();
} catch (InvocationTargetException e) {
    // 생성자에 접근할 수 없을 때
    e.printStackTrace();
} catch (ClassCastException e) {
    // Set을 구현하지 않은 클래스일 때
    e.printStackTrace();
}

// s가 무엇이냐에 따라서 출력 결과가 달라진다.
s.addAll(List.of("안녕", "하세", "요", "."));
System.out.println("s = " + s);
```

**출력 결과**

- `“java.util.LinkedHashSet”` : 순서를 지킨다.
- `“java.util.HashSet”` : 순서를 지키지 않는다.

```java
// LinkedHashSet
s = [안녕, 하세, 요, .]
// HashSet
s = [요, 안녕, 하세, .]
```

### 테스트 코드의 단점

1. 런타임에 여섯 가지나 되는 예외가 던져질 수 있다.
2. 클래스 이름만으로 인스턴스를 생성하기 위해 무수히 긴 코드를 작성해야 한다.

## 핵심 정리

- 리플렉션은 복잡한 특수 시스템을 개발할 때 필요한 강력한 기능이지만, 단점도 많다.
- 컴파일타임에는 알 수 없는 클래스를 사용하는 프로그램을 작성한다면 리플렉션을 사용해야 할 것이다.
- 단, 되도록 객체 생성에만 사용하고, 생성한 객체를 이용할 때는 적절한 인터페이스나 컴파일타임에 알 수 있는 상위 클래스로 형변환해 사용해야 한다.