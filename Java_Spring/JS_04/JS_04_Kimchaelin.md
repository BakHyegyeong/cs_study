# 제네릭(Generic)이란
## 메서드나 클래스에서 사용할 타입을 미리 지정하는 방법
- ex) ArrayList<String> list = new ArrayList<String>(); -> ArrayList에 String 타입만 저장 가능 
## 장점
  - 타입을 컴파일 시에 체크하기 때문에 타입 안정성을 제공해준다.
  - 타입 변환을 해주지 않아도 된다.
    ```java
        List list = new ArrayList();
        list.add("hello");
        String str = (String) list.get(0); // 타입 변환 필요
      
        List<String> list = new ArrayList<String>();
        list.add("hello");
        String str = list.get(0); // 타입 변환 필요 없음
    ```
  - 타입 범위를 제한할 수 있어서 다양한 타입을 사용할 수 있다.
    - extends : 해당 클래스와 하위 클래스만 사용 가능 ex) Set<T extends Number>
    - super : 해당 클래스와 상위 클래스만 사용 가능 ex) List<T super Number>
    - ? : 모든 타입 사용 가능 ex) Collection<?>
    - interface : 해당 인터페이스를 구현한 클래스만 사용 가능 ex) List<T extends interface>
    
## 단점
- 타입을 지정해주지 않으면 컴파일 시에 경고가 발생한다.
- 타입을 지정해주지 않으면 Object로 처리되어서 타입 변환을 해주어야 한다.
## 특징 
- 타입 소거 : 제네릭은 1.5 버전부터 지원하므로 컴파일 시에만 유효하므로, 이전 버전과 호환성을 위해 런타임 시에는 타입이 소거된다.
  - ex) List<String> list = new ArrayList<String>(); -> List list = new ArrayList(); (Type erasure(**타입 소거**))
- 무공변 : 제네릭 타입이 다른 경우 서로 호환되지 않는다.
  - ex) List<Object> list = new ArrayList<String>(); -> 에러
  - 해결 방법 : 와일드 카드 사용 ex) List<? extends Object> list = new ArrayList<String>();
- 와일드 카드 : 제네릭 타입을 더 유연하게 사용할 수 있게 해준다.
  - ex) List<?> list = new ArrayList<String>(); -> 가능
  - 종류 : <?>, <? extends T>, <? super T>


## 무공변 vs 공변 vs 반공변
- 무공변 : 서로 다른 제네릭 타입은 호환되지 않는다. S가 T의 하위 클래스라고 해도 List<S>와 List<T>는 상속 관계가 아니다.
  - ex) List<Object> list = new ArrayList<String>(); -> 에러
- 공변 : A가 B의 하위 클래스일 때, List<A>는 List<B>의 하위 클래스가 된다.
  - ex) List<Object> list = new ArrayList<String>(); -> 가능
- 반공변 : A가 B의 하위 클래스일 때, List<A>는 List<B>의 상위 클래스가 된다.
