# 아이템 19 : 상속 금지

# 상속을 위한 좋은 설계

## API 문서

상속은 코드 재사용을 쉽게 해주지만 잘못 사용하면 **오류가 발생**하고 메서드 호출과 달리 **캡슐화를 깨뜨린다.**

→ 상속용 클래스는 **재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지 문서**로 남겨야 한다.

자바 8에 도입된 `@implSpec` 태그를 붙여주면 자바독 도구가 다음과 같이 자동으로 생성해준다.

```java
/**
 * {@inheritDoc}
 *
 * @implSpec
 * This implementation returns {@code size() == 0}.
 */
public boolean isEmpty(){
    return size()==0;
}
```

`implementation Requirements` 부분은 메서드 내부의 동작 방식을 설명해준다.

### API 문서 내용

본래 좋은 API 문서라면 **'어떻게'가 아니라 '무엇'**을 설명하는 것이 좋다.

하지만 상속은 캡슐화를 깨뜨리는 방식이라 어쩔 수 없다.

- 재정의 가능여부
- 호출 순서
- 호출 결과의 영향

## 상속 설계 시 hook을 주의하자

- hook : 클래스의 내부 동작 과정 중간에 끼어들 수 있는 코드
- hook 메서드 : 기본 클래스에서 정의되지만, **하위 클래스에서 오버라이드하여 기본 동작을 조정하거나 확장**할 수 있게 하는 메서드
    
    → 클래스의 내부 동작 과정 중간에 끼어들 수 있는 지점으로, 하위 클래스가 필요에 따라 구현을 변경할 수 있는 기회를 제공
    

상속을 설계할 때 훅을 잘 선별해 protected 메서드 형태로 공개한다.

→ protected 메서드로 선언하여 하위 클래스만 접근할 수 있게 해야한다.

→ 이 부분에 대해서 정확한 기준은 없고 잘 예측해야 한다.

```java
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {
		
		public void clear() {
        removeRange(0, size());
    }
		
		protected void removeRange(int fromIndex, int toIndex) {
        ListIterator<E> it = listIterator(fromIndex);
        for (int i = 0, n = toIndex - fromIndex; i < n; i++) {
            it.next();
            it.remove();
        }
    }
}
```

- `clear()`
    - `removeRange()`를 호출해 index 처음부터 끝까지 삭제
- `removeRange()`
    - `clear()`를 고성능으로 만들기 쉽게 하기 위해 제공
        
        → `ListIterator`를 사용하여 효율적으로 요소를 제거할 수 있다.
        
    - 해당 메서드가 없었다면 하위 클래스에서 clear 메서드 호출 시 성능이 느려지거나 새로 구현했어야 함

### protected로 노출해야하는 메서드 선택방법

- 가능한 숫자가 적어야 한다. 다만, 너무 적게 노출해서 상속으로 얻는 이점을 없애는일이 없도록해야 한다.
- 상속용 클래스를 시험하는 방법은 직접 하위클래스를 만드는 방법이 유일하다.
    - 하위 클래스 검증 개수로는 3개정도가 적정하다.
- 꼭 필용한 `protected` 멤버를 놓쳤다면 하위 클래스 작성시 그 빈자리가 드러난다.
- 하위 클래스를 여러개 만들 때까지 전혀 쓰이지않는 `protected` 멤버는 사실 `private`이어야 할 가능성이 크다.

## 상속용 클래스의 생성자

**상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.**

→ 상위 클래스의 생성자가 하위 클래스 생성자보다 먼저 실행되므로, **다형성**으로 인해 **하위 클래스에서 재정의한 메서드가 하위 클래스의 생성자보다 먼저 호출**되기 때문이다.

- 상위 클래스
    
    ```java
    public class Super {
        // 잘못된 예 - 생성자가 재정의 가능 메서드를 호출
        public Super() {
            overrideMe();
        }
    
        public void overrideMe() {
        }
    }
    ```
    

- 하위 클래스
    
    ```java
    public final class Sub extends Super {
        // 초기화되지 않은 final 필드. 생성자에서 초기화
        private final Instant instant;
    
        Sub() {
            instant = Instant.now();
        }
    
        // 재정의 가능 메서드. 상위 클래스의 생성자가 호출
        @Override public void overrideMe() {
            System.out.println(instant);
        }
    
        public static void main(String[] args) {
            Sub sub = new Sub();
            sub.overrideMe();
        }
    }
    ```
    

상위 클래스의 생성자는 **하위 클래스의 생성자가 인스턴스 필드를 초기화하기도 전에** `overrideMe()`를 호출하기 때문에 `instant` 가 아닌 `null` 값이 출력된다. 만약, `instant` 객체의 메서드를 호출하려 했다면 `NullPointerException` 이 발생했을 것이다.

## clone과 readObject 메서드

**clone과 readObject 메서드 모두 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다.**

→ 두 메서드 모두 새로운 객체를 생성하기 때문에 **생성자와 비슷한 효과**를 낸다. 

클래스의 상태가 초기화되기 전에 메서드부터 호출되는 상황이 발생할 수 있다.

따라서, `Clonable`과 `Serializabl` 인터페이스 중 하나라도 구현한 클래스를 상속할 수 있게 설계하는 것은 좋지 않다.

> ⭐<br> - clone : 복제본의 상태를 올바르게 수정하기 전에 재정의한 메서드 호출 <br> - readObject : 하위 클래스가 역질렬화 되기 전에 재정의한 메서드부터 호출


### **Serializable의 readResolve, writeReplace 메서드**

 **Serializable을 구현한 상속용 클래스가 readResolve나 writeReplace 메서드를 갖는다면 private가 아닌 protected로 선언해야 한다.**

해당 메서드들은 내부 구현을 공개하지 않기 위해 기본적으로 `private` 으로 선언한다. 직렬화와 역직렬화 특성상 보안에 매우 취약하기 때문이다. 

하지만, 상속을 하려 한다면 `protected` 으로 접근 제한을 한단계 낮춰야 하는 문제가 발생한다.

## 클래스 상속시 주의사항

클래스를 상속용으로 설계하려면 엄청난 노력이 들고 그 클래스에 안기는 제약도 상당하다.

- 추상 클래스나 인터페이스의 골격 구현 : 상속 사용
- 불변 클래스 : 상속 사용 X

일반적인 구체 클래스는 `final`도 아니고 **상속용으로 설계되거나 문서화되지 않아 클래스 변화시 오작동**이 발생할 가능성이 있다.

→ 따라서 **상속용으로 설계하지 않은 클래스는 상속을 금지**해야 한다.

### 상속을 금지하는 방법

1. 클래스를 final로 선언
2. 모든 생성자를 private나 default로 선언 뒤 public 정적 팩토리 생성

## 최종 정리

상속용 클래스를 설계하려면 클래스 내부에서 스스로를 어떻게 사용하는지(자기사용 패턴) 모두 문서로 남겨야 하며, 일단 문서화한 것은 그 클래스가 쓰이는 한 반드시 지켜야 한다.

만약, 이를 지키지 않으면 문서를 믿고 활용하던 하위 클래스를 오작동하게 만들 수 있다.

또한, 하위 클래스를 효율적으로 만들수 있도록 `protected` 접근제어자를 갖는 일부 메서드를 제공해야 할 수도 있다. 그러니 클래스를 확장해야 할 명확한 사유가 없다면 상속을 금지하는 편이 낫다.

상속을 금지하려면 클래스를 `final`로 선언하거나 생성자를 외부에서 접근할 수 없도록 하면된다.