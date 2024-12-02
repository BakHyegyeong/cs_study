# Comparable과 Comparator의 차이
자바에서 객체를 정렬하기 위해서 쓰는 인터페이스이다. 

```java
// Comparable 사용
class Student implements Comparable<Student> {
    private String name;
    private int score;

    public Student(String name, int score) {
        this.name = name;
        this.score = score;
    }

    @Override
    public int compareTo(Student o) {
        return this.score - o.score; // 점수 기준 오름차순
    }
}

// Comparator 사용
class NameComparator implements Comparator<Student> {
    @Override
    public int compare(Student o1, Student o2) {
        return o1.name.compareTo(o2.name); // 이름 기준 오름차순
    }
}
```

## Comparable
- 객체 클래스 내부에서 직접 정의
- compareTo(param)
- 기본 정렬 기준
- ex. `Collections.sort(list)`

## Comparator
- 별도의 외부 클래스에서 정의
- compare(param1, param2)
- 사용자 정의 정렬 기준
- ex. `Collections.sort(list, new ComparatorImpl())`


# HashMap의 특징과 동작 방식
- 키-값 으로 저장되고, 중복(값)/Null이 허용된다. 
- 순서가 없이 저장된다.
- 검색 시간이 O(1)로 빠르다.
- 멀티 스레드 환경에서 안전하지 않다. (ConcurrentHashMap을 사용해야 함)

### **List, Set, Map의 차이**

| **특징**          | **List**                                | **Set**                                    | **Map**                                 |
|--------------------|-----------------------------------------|--------------------------------------------|-----------------------------------------|
| **저장 방식**       | 순서대로 저장                          | 순서 없이 저장                              | 키-값(Key-Value) 쌍으로 저장             |
| **중복 허용 여부**   | **허용**                               | **허용하지 않음**                           | 키는 중복 불가, 값은 중복 가능            |
| **접근 방식**       | 인덱스를 사용하여 접근 가능             | 요소를 직접 검색                            | 키를 사용하여 값에 접근 가능             |
| **정렬 여부**       | 기본적으로 정렬되지 않음               | `TreeSet`을 사용하면 정렬 가능              | `TreeMap`을 사용하면 키 기준으로 정렬     |
| **주요 구현체**     | `ArrayList`, `LinkedList`              | `HashSet`, `TreeSet`, `LinkedHashSet`      | `HashMap`, `TreeMap`, `LinkedHashMap`   |
| **사용 목적**       | 순서가 중요한 데이터 관리              | 중복을 제거하며 유일한 값 관리               | 키와 값을 맵핑하여 데이터 관리            |

