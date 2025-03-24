# 아이템 87 : 커스텀 직렬화 형태를 고려해보라

# Serializable을 구현해야할 때

- 기본 직렬화 형태는 유연성, 성능, 정확성 측면에서 신중히 고민한 후 합당할 때만 사용해야 한다.
    
    → 객체의 물리, 독립된 논리적인 모습만 표현해야 한다.
    
- 객체의 물리적 표현과 논리적 내용이 같다면 기본 직렬화 형태라도 무방하다.
    
    → 물리적 표현 - 논리적 내용 : `String MiddleName;` - 중간이름 형태로 그 의미가 바로 해석되는 경우를 기본 직렬화 형태를 사용해도 무방하다.
    
- 기본 직렬화 형태가 적합하다고 결정했더라도 불변식 보장과 보안을 위해 readObject 메서드를 제공해야 할 때가 많다.
    
    → readObject 메서드가 필드가 null이 아님을 보장해야 한다.
    
- private임에도 직렬화 형태에 포함되는 공개 API에 속하기 때문에 문서화 주석을 달아주어야 한다.

## **기본 직렬화 형태에 적합하지 않은 예**

이 클래스는 논리적으로는 **일련의 문자열을 표현**하며, 물리적으로는 **문자열들을 이중 연결 리스트로 연결**한다.

```java
public final class StringList implements Serializable {
    private int size = 0;
    private Entry head = null;

    private static class Entry implements Serializable {
        String data;
        Entry next;
        Entry previous;
    }
    // ... 생략
}
```

- 객체의 물리적 표현과 논리적 표현의 차이가 클 때 기본 직렬화 형태를 사용하면 아래의 4가지 문제가 생긴다.
    - 공개 API가 현재의 내부 표현 방식에 영구히 묶인다.
    - 많은 공간을 차지할 수 있다.
    - 시간이 많이 소요될 수 있다.
    - 스택 오버플로를 일으킬 수 있다.
- 이 클래스에 기본 직렬화 형태를 사용하면 **각 노드의 양방향 연결 정보를 포함해 모든 엔트리를 출력**한다.

### 해결 방법

리스트가 포함한 문자열 개수를 적고, 문자열들을 나열하는 수준의 **논리적인 구성만 담는다.**

```java
public final class StringList implements Serializable {
	// 직렬화 대상에서 제외한다.
    private transient int size = 0;
    private transient Entry head = null;

    // 이번에는 직렬화 하지 않는다.
    private static class Entry {
        String data;
        Entry next;
        Entry previous;
    }

    // 문자열을 리스트에 추가한다.
    public final void add(String s) { ... }

    /**
     * StringList 인스턴스를 직렬화한다.
     */
    private void writeObject(ObjectOutputStream stream)
            throws IOException {
        stream.defaultWriteObject();
        stream.writeInt(size);

        // 모든 원소를 순서대로 기록한다.
        for (Entry e = head; e != null; e = e.next) {
            s.writeObject(e.data);
        }
    }

    private void readObject(ObjectInputStream stream)
            throws IOException, ClassNotFoundException {
        stream.defaultReadObject();
        int numElements = stream.readInt();

				// 모든 원소를 읽어 이 리스트에 삽입한다.
        for (int i = 0; i < numElements; i++) {
            add((String) stream.readObject());
        }
    }
    // ... 생략
}
```

- 이때 필드 모두가 transient더라도 writeObject / readObject에서 가장 먼저 **defaultWriteObject / defaultReadObject를 호출해야 한다.**
    - 향후 transient가 아닌 인스턴스 필드가 추가되어도, 구버전과 호환성이 깨지지 않는다.
    - 구버전에서 defaultReadObject를 호출하지 않으면, 신버전을 역직렬화 할 때 StreamCorruptedException이 발생한다.

- **transient 한정자 :** 해당 인스턴스 필드가 **기본 직렬화 형태에 포함되지 않는다**는 표시이다.
    - **해당 객체의 논리적 상태와 무관한 필드에만 붙여야 한다.**
    - 다른 필드에서 유도되는 필드(캐시된 해시값), JVM을 실행할 때마다 값이 달라지는 필드 모두 포함된다.
    - transient 필드는 기본 직렬화로 역직렬화하면 기본값(0, false, null)로 초기화 된다.
        
        → 기본값을 그대로 사용하면 안 되는 경우에는  defaultReadObject() 메서드를 통해 해당 필드의 값을 복원하거나 처음 사용할 때 초기화해야 한다.
        

### **불변식이 세부 구현에 따라 달라지는 객체에서의 역직렬화**

Hash Table은 물리적으로는 키-값 엔트리들을 담은 해시 버킷을 차례로 나열한 형태이다.

- Hash Table이 어떤 엔트리를 어떤 버킷을 담을 지는 Key에서 구한 hashcode로 결정한다.
    
    → 해당 hashcode는 구현에 따라, 심지어 계산할 때마다 달라지기도 한다.
    
- 애초에 불변식이 세부 구현에 따라 달라지므로, 기본 직렬화 방식을 사용하면 불변식이 심각하게 훼손될 수 있다.

## **동기화 메커니즘 직렬화**

기본 직렬화 사용 여부와 상관없이 객체의 전체 상태를 읽는 메서드에 적용해야 하는 **동기화 메커니즘을 직렬화에도 적용해야 한다.**

- 모든 메서드를 synchronized로 선언하여 스레드 안전하게 만든 객체에서 기본 직렬화를 사용하려면 **writeObject도 다음 코드처럼 synchronized로 선언해야 한다.**
    
    ```java
    private synchronized void writeObject(ObjectOutputStream stream)
            throws IOException {
        stream.defaultWriteObject();
    }
    ```
    

- writeObject 메서드 안에서 동기화 하려면 **클래스의 다른 부분에서 사용하는 Lock 순서를 똑같이 따라야 한다.**
    
    → 그러지 않으면 **자원 순서 교착상태**에 빠질 수 있다.
    

## 직렬 버전 UID

직렬화 가능 클래스에 직렬 버전 UID를 명시적으로 부여해야 한다.

- 직렬 버전 UID가 일으키는 잠재적인 호환성 문제가 사라진다.
- 직렬 버전 UID를 명시하면 런타임에 생성할 필요가 없어 성능이 좋아진다.

### 사용 방법

각 클래스에 아래 코드를 추가하면 된다.

- 고유한 값이 아닌 아무 값이나 선택해도 된다.
- 이미 구버전으로 직렬화된 인스턴스가 있다면, 구버전 클래스를 serialver 유틸리티에 입력으로 주어 얻어낸 **자동 생성된 값을 그대로 사용해야 한다.**
- 구버전으로 직렬화된 인스턴스와의 호환성을 끊는 경우를 제외하고 직렬 버전 UID를 수정하면 안된다.

```java
private static final long serialVersionUID = <무작위로 고른 long 값>;
```

## 핵심 정리

- 자바의 기본 직렬화 형태는 객체를 직렬화한 결과가 해당 객체의 논리적 표현에 부합할 때만 사용하고, 그렇지 않다면 객체를 적절히 설명하는 커스텀 직렬화 형태를 고려하는 것이 좋다.
- 직렬화 형태도 공개 메서드를 설계하는 것처럼 시간을 들여 설계해야 한다.
- 한번 공개된 메서드는 향후 릴리스에서 제거할 수 없듯이, 직렬화 형태에 포함된 필드도 마음대로 제거할 수 없다.
    
    → 직렬화 호환성을 위해 계속해서 지원해야 한다.
    
- 잘못된 직렬화 형태를 선택하면 해당 클래스의 복잡성과 성능에 영구히 부정적인 영향을 미친다.