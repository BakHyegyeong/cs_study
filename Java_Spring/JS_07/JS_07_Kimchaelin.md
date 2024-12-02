# Comparable과 Comparator의 차이
## Comparable
- 자신과 다른 객체를 비교
- 클래스 내부에서 정렬 기준을 구현하는데 사용
    - 객체가 정렬을 정의되어있음.
- `compareTo()` 메소드를 구현해야 함
- ex. `Collections.sort(list)`
```java
class Student implements Comparable<Student> {
    int age;
    int name;

    Student(int age, int classNumber) {
        this.age = age;
        this.classNumber = classNumber;
    }

    @Override
    public int compareTo(Student o) {
        // 자기자신의 age가 o의 age보다 크다면 양수
        if(this.age > o.age) {
            return 1;
        }
        // 자기 자신의 age와 o의 age가 같다면 0
        else if(this.age == o.age) {
            return 0;
        }
        // 자기 자신의 age가 o의 age보다 작다면 음수
        else {
            return -1;
        }
    }
}
// 출처: https://sunrise-min.tistory.com/entry/Java-Comparable과-Comparator의-차이 [내가 보기 위한 기록:티스토리]
```

## Comparator
- 다른 두 객체를 비교할 때 사용
- 클래스 외부에서 정렬 기준을 구현하는데 사용
  - 별도 클래스로 구현 or 익명 클래스로 구현 or 람다식으로 구현
- `compare()` 메소드를 구현해야 함
- ex. `Collections.sort(list, new ComparatorImpl())`
```java
class AgeComparator implements Comparator<Student> {
    @Override
    public int compare(Student o1, Student o2) {
        // o1의 age가 o2의 age보다 크다면 양수
        if(o1.age > o2.age) {
            return 1;
        }
        // o1의 age와 o2의 age가 같다면 0
        else if(o1.age == o2.age) {
            return 0;
        }
        // o1의 age가 o2의 age보다 작다면 음수
        else {
            return -1;
        }
    }
}
// 출처: https://sunrise-min.tistory.com/entry/Java-Comparable과-Comparator의-차이 [내가 보기 위한 기록:티스토리]
```

###### #익명 클래스와 람다 표현식
```java
// 익명 클래스
Collections.sort(list, new Comparator<Student>() {
    @Override
    public int compare(Student o1, Student o2) {
        return o1.age - o2.age;
    }
});

// 람다 표현식
Collections.sort(list, (o1, o2) -> o1.age - o2.age);
```
# HashMap의 특징과 동작 방식
- Key-Value 쌍으로 데이터를 저장하며, Key를 해시 함수에 넣어 해시값을 계산한 후 배열의 인덱스로 변환하여 값을 저장
- 키와 값 모두 null 허용 (단, 키는 하나의 Null만 가능)
- 평균적으로 삽입/조회/삭제는 O(1)의 시간복잡도를 가짐(해시값으로 바로 접근 가능)
- 멀티 스레드 환경에서 안전하지 않다. (ConcurrentHashMap을 사용해야 함) -> 동기화 처리를 하지 않음
###### ConcurrentHashMap : 락을 사용하여 동기화 처리를 하여 멀티 스레드 환경에서 안전하게 사용할 수 있는 클래스
# List, Set, Map의 차이

<img src="https://velog.velcdn.com/images/maestroks/post/0e1860eb-b02e-4e7a-973c-ca7fe2e0a45a/image.jpg" width="70%">

| 특성          | List                          | Set                                     | Map                                   |
|---------------|-------------------------------|-----------------------------------------|---------------------------------------|
| **중복 허용**  | **허용**                        | **허용하지 않음**                             | 키는 중복 불가, 값은 중복 가능                    |
| **저장 방식**  | 삽입 순서대로 저장                    | 순서 없이 저장, LinkedHashSet(삽입 순서대로 저장하기 위한 set) | 키-값(Key-Value) 쌍으로 저장, LinkedHashMap(삽입 순서대로 저장하기 위한 set) |
| **접근 방식**  | 임의 접근 가능(인덱스)                 | iterater를 이용해서 접근                       | 키를 사용하여 값에 접근 가능                      |
| **주요 구현체** | ArrayList, LinkedList, Vector | HashSet, TreeSet, LinkedHashSet         | HashMap, TreeMap, LinkedHashMap |
| **용도**| 들어온 순서를 중시하거나 중복 허용이 필요한 경우   | 중복을 방지하고 고유 요소를 저장할 때                   | 키-값 쌍을 저장할 때 |
|**예시**|학생의 성적을 저장할 때| 유일한 과목을 저장할 때 |학생의 ID와 성적을 저장할 때|
