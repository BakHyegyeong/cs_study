# 아이템 13 : clone 재정의는 항상 주의해서 진행하라

# clone

: **객체의 모든 필드를 복사하여 새로운 객체에 넣어 반환**하는 동작을 수행하는 메서드

→ 필드의 값이 같은 객체를 새로 만드는 것

→ 다만 clone메서드의 경우는 **얕은 복사**를 기반으로 한다.

### 얕은 복사 vs 깊은 복사

- 얕은 복사 : 값 자체를 복사하는 것이 아니라 주소값을 복사하여 객체가 같은 메모리를 가리키게 한다.
    
    → 복제된 인스턴스가 메모리에 새로 생성되지 않는다.
    
- 깊은 복사 : 실제 값을 새로운 메모리 공간에 복사하는 방식이다.

## Cloneable

: **복제해도 되는 클래스임을 명시**하는 용도의 `maker interface`이다.

→ clone메서드는 Cloneable안에 있는 것이 아닌 `java.lang.Object` 클래스 내부에 `protected`메서드로 구현되어 있다.

```java
public interface Cloneable {
}
```

### 사용 예시

: **Cloneable을 구현하는 클래스**가 clone메서드를 오버라이딩하면 **객체와 같은 크기의 메모리를 할당**하고, **그 객체의 필드들을 복사한 객체를 반환**한다.

→ Cloneable를 구현하지 않는 클래스가 clone을 호출하면 `CloneNotSupportedException` 오류를 발생시킨다.

```java
public example implements Cloneable {
	...
	
	@Override // clone() 구현
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

}
```

## clone 재정의시 문제점

### 1. 생성자로 객체를 생성할 경우

<aside>
⭐

**생성자를 호출하지 않고도 객체를 생성**할 수 있다.

</aside>

`clone`메서드가 `super.clone`메서드가 아닌, 생성자를 호출해 얻은 인스턴스를 반환해도 컴파일시에 문제가되지 않는다. 

하지만 이 클래스의 하위 클래스에서 `super.clone`메서드를 호출한다면 잘못된 클래스의 객체가 만들어져 결국 하위 클래스의 `clone`메서드가 제대로 작동하지 않게된다.

```java
class Dog extends Animal {
    String breed;

    public Dog(String name, String breed) {
        super(name);
        this.breed = breed;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        // 잘못된 예시: super.clone() 대신 생성자를 호출
        Dog clonedDog = new Dog("Unknown", this.breed); // 잘못된 접근
        clonedDog.name = this.name; // 상위 클래스 필드 수동 복사
        return clonedDog;
    }
}
```

위의 예시에서 super.clone가 아닌 생성자를 호출해 객체를 생성한다면 name 필드의 값은 기본값으로 생성되었다가 다시 수동으로 부모 클래스의 값이 할당된다.

이는 객체를 생성할 때 의도가 명확하지 않고, 코드 유지보수 시 혼란을 초래할 수 있다는 문제점과 더불어 상위 클래스의 필드 값이 제대로 복사되지 않을 수 있다는 문제점이 있다.

### 2. **가변 객체를 참조**

clone 메서드를 사용할 때 주의할 점은 얕은 복사가 되다는 점이다.

→ 클래스가 가변 객체를 참조하는 순간 오류가 발생할 수 있고, **객체의 불변성을 보장할 수 없다.**

아래와 같은 방법으로 clone을 재정의한다면 불변성을 보장할 수 있다.

```java
@Override
protected Stack clone() {
        try {
            Stack result = (Stack) super.clone();
            result.elements = elements.clone();
            return result;
        } catch (CloneNotSupportedException cloneNotSupportedException) {
            throw new AssertionError();
        }
}
```

만약, `clone` 메서드가 단순히 `super.clone`결과를 반환한다면 반환된 `Stack` 인스턴스의 `size`는 올바른 값을 갖겠지만, `elements`는 원본 `Stack` 인스턴스와 똑같은 배열을 참조하게 될것이다.

→ 그렇기에 `Stack` 내부의 `elements` 배열의 clone을 다시 진행한다.

**→ 배열을 복제할때는 `clone` 사용이 권장되는데 
배열 복제는 `clone` 기능이 제대로 사용되는 유일한 예라 할 수 있다.**

### 복잡한 가변 객체를 참조

```java
public class HashTable implements Cloneable {
    private Entry[] buckets;

    private static class Entry {
        final Object key;
        Object value;
        Entry next;

        Entry(Object key, Object value, Entry next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
	
    // getter, setter ...
}
```

단순하게 **`clone()`**을 사용하면 **복사본이 원본의 엔트리를 통해서 값을 찾기 때문에 잘못된 값을 찾게 된다**.

아래와 같은 방법으로 재정의해 해결한다.

```java
Entry clone() {
    Entry result = new Entry(key, value, next);
    for (Entry p = result; p.next != null; p = p.next) {
        p.next = new Entry(p.next.key, p.next.value, p.next.next);
    }
    return result;
}
```

### 상속 가능한 객체

상속 가능한 객체는 기존 **`Object`** 방식(protected, throwable)을 따라서 구현할 수도 있고 동작을 못하게 막을 수도 있다.

하지만 상속 가능한 객체, 즉 **상속 클래스는 Cloneable을 구현해서는 안 된다**.

## clone 메서드 일반 규약

1. `x.clone() != x`
    
    이 식은 참이다.
    
2. `x.clone().getClass() == x.getClass()`
    
    이 식역시 참이다. 하지만 반드시 만족해야 하는 것은 아니다.
    
3. `x.clone().getClass().equals(x)`
    
    이 식은 참이지만 필수는 아니다.
    
4. `x.clone().getClass() == x.getClass()`
    
    만약, `super.clone()` 을 호출해 얻은 객체를 `clone()`이 반환한다면 이 식은 참이다. 또한, 관례상 **반환된 객체와 원본객체는 독립적**이어야 한다. 이를 만족하려면 `super.clone()`으로 얻은 객체의 필드 중 하나 이상을 반환 전에 수정해야 할 수도 있다
    

## clone 대신 복사 생성자, 복사 팩토리

- 복사 생성자 : 복사 생성자는 해당 클래스의 인스턴스를 인자로 받는 생성자
→ 이 생성자는 전달받은 인스턴스의 상태를 복사하여 새로운 인스턴스를 초기화한다.
복사 생성자는 객체의 깊은 복사를 명시적으로 제어할 수 있게 해준다.
- 복사 팩토리 : 복사 팩토리 메서드는 복사 생성자와 유사한 기능을 제공하지만, 메서드를 통해 구현된다.
→ 이 방식은 클래스의 인스턴스를 인자로 받아, 그 인스턴스의 상태를 복사하여 새로운 인스턴스를 반환한다.

```java
// 변환 생성자
public class MyClass {
    private int data;

    public MyClass(MyClass source) {
        this.data = source.data;
    }
}

// 변환 팩토리
public class MyClass {
    private int data;

    public static MyClass newInstance(MyClass source) {
        return new MyClass(source.data);
    }
}
```

https://velog.io/@alkwen0996/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EC%95%84%EC%9D%B4%ED%85%9C13-clone-%EC%9E%AC%EC%A0%95%EC%9D%98%EB%8A%94-%ED%95%AD%EC%83%81-%EC%A3%BC%EC%9D%98%ED%95%B4%EC%84%9C-%EC%A7%84%ED%96%89%ED%95%98%EB%9D%BC

https://stdio-han.tistory.com/136

https://codinghejow.tistory.com/332

- ArrayList의 clone
    
    https://hianna.tistory.com/567