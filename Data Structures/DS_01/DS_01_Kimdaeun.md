# Array와 ArrayList의 차이점

배열은 고정크기이고 생성 후에는 크기를 변경할 수 없다.

ArrayList는 동적으로 크기가 변하고 데이터를 추가할 때 마다 자동으로 조정이 된다.

```java
// 1. Array 선언 및 초기화
int[] numbers = new int[5];
numbers[0] = 10;
numbers[1] = 20;
// 크기 변경 불가, 배열에 추가하려면 새로운 배열을 만들어야 함

// 2. ArrayList 선언 및 초기화
List<Integer> numberList = new ArrayList<>();
numberList.add(10);
numberList.add(20);
numberList.add(30);
// 크기 동적 조정 가능, 요소 추가 시 자동으로 확장
```

# **ArrayList의 크기 조정 방식**

**ArrayList**는 내부적으로 배열을 사용하고, 용량이 초과될 경우 더 큰 배열을 생성하고 기존 데이터를 새 배열에 복사하여 크기를 동적으로 조정한다.

1. **새로운 배열 생성** : 현재 용량의 약 1.5배 또는 2배 크기의 새로운 배열이 생성된다.
2. **데이터 복사** : 기존 배열의 모든 데이터를 새로운 배열로 복사한다.
3. **참조 변경** : ArrayList가 새로운 배열을 참조하도록 설정한다.

## ArrayList 동적 크기 조정

```java
// ArrayList 생성 및 데이터 추가
ArrayList<Integer> list = new ArrayList<>(10); // 초기 용량이 10인 ArrayList 생성
for (int i = 0; i < 15; i++) {
    list.add(i); // 10을 초과하도록 요소를 계속 추가하면 새로운 배열이 생성
}
```

- ArrayList는 초기 용량보다 많은 요소가 추가될 경우 새로운 배열이 생성되고 기존 요소들이 모두 복사된다. 이 과정에서 잠시 동안 메모리 사용량이 증가할 수 있다.
- 초기 용량은 ArrayList가 데이터를 저장하기 위해 처음부터 할당하는 내부 배열의 크기를 지정하는 것이지 요소의 최대 개수를 제한하는 것은 아니다.

# **Array와 ArrayList는 어느 상황에서 더 적합한가?**

**Array**는 **고정 크기와 빠른 접근이 필요한 상황**에 적합하다.

**ArrayList**는 **자주 변경되는 크기와 요소 삽입 및 삭제가 많은 상황**에 적합하다.

```java
// 고정된 학생 점수 리스트
int[] scores = {90, 85, 78, 92, 88};
// 크기가 고정된 경우 배열이 효율적

// 학생 이름을 동적으로 추가하는 상황
List<String> studentNames = new ArrayList<>();
studentNames.add("John");
studentNames.add("Sarah");
// 학생 목록이 계속 변하는 상황에서 ArrayList가 효율적
```

**→ Array**는 메모리 효율이 높고 고정된 크기의 데이터 관리에 적합하지만 크기 변경 불가

**→ ArrayList**는 동적으로 크기를 변경할 수 있어 다양한 데이터 삽입/삭제가 필요한 경우에 유리
