## 1. Hash Table이 평균적으로 O(1) 성능을 유지하는 이유

Hash Table은 해시 함수를 사용하여 키를 배열의 인덱스로 변환함으로써, 직접 해당 인덱스에 접근하여 값을 저장하거나 찾을 수 있다.

-> **검색, 삽입, 삭제 작업이 평균적으로 O(1)** 의 시간 복잡도

- 충돌이 없는 경우 : 각 데이터는 고유한 인덱스에 저장되므로 해시 충돌이 없다면 항상 해당 위치로 직접 접근할 수 있다.
- 해시 함수의 품질 : 해시 함수가 잘 설계되어 있다면 키 값들이 균등하게 분포되어 충돌이 최소화되어 평균 성능이 O(1)로 유지된다.

but 해시 충돌이 발생하거나 해시 테이블의 용량이 제한적일 경우 특정 상황에서 시간 복잡도가 O(n)에 가까워질 수 있다.

## 2. Hash Collision을 해결하는 방법
Hash Collision은 서로 다른 키들이 동일한 해시 값을 갖는 경우를 의미한다. 

#### 체이닝의 작동 방식
1. **해시 함수로 인덱스 계산**  
   데이터를 해시 함수에 넣어 해당 데이터의 인덱스를 계산
2. **충돌 발생 시 연결 리스트에 추가**  
   만약 이미 해당 인덱스에 데이터가 있는 경우, 기존 데이터에 새 데이터를 연결 리스트로 연결하여 추가
3. **탐색 및 검색**  
   데이터 검색 시, 해시 함수를 사용해 해당 인덱스로 이동한 후 연결 리스트의 노드를 순차적으로 확인하면서 데이터를 찾는다.

#### 체이닝 예제 (Java에서 `HashMap`)
Java의 `HashMap`은 기본적으로 체이닝 방식을 사용하여 해시 충돌을 처리한다.
```java
import java.util.HashMap;

public class HashMapExample {
    public static void main(String[] args) {
        HashMap<String, Integer> map = new HashMap<>();

        // 데이터 삽입
        map.put("Alice", 30);
        map.put("Bob", 25);
        map.put("Charlie", 35);

        // 데이터 검색
        System.out.println("Alice's age: " + map.get("Alice"));
        System.out.println("Bob's age: " + map.get("Bob"));
    }
}
```

## 3. HashMap과 HashSet의 차이점
| 특성           | HashMap                                         | HashSet                                  |
|----------------|-------------------------------------------------|------------------------------------------|
| **저장 형태**    | Key-Value Pair (키와 값 쌍)                      | 단일 Value (값만 저장)                    |
| **키/값의 유일성** | 키는 유일, 값은 중복 가능                            | 모든 값이 유일                             |
| **Null 허용 여부** | 키와 값 모두 허용 (단, 키는 하나의 Null만 가능)       | 한 개의 Null 값만 허용                     |
| **내부 구조**    | HashMap은 체이닝을 사용하여 키를 해시 처리 후 저장     | HashSet은 내부적으로 HashMap을 사용하여 키를 저장 |
| **적용 예시**    | 데이터의 키-값 매핑 필요 시 (예: 학생 ID-성적 저장)      | 중복을 피한 고유 값 모음 필요 시 (예: 유일한 태그 집합) |


```	•	HashMap 예시: 이름을 키로, 나이를 값으로 저장
HashMap<String, Integer> ageMap = new HashMap<>();
ageMap.put("Alice", 25);
ageMap.put("Bob", 30);
```

```	•	HashSet 예시: 유일한 학번을 저장하는 집합
HashSet<Integer> studentIds = new HashSet<>();
studentIds.add(12345);
studentIds.add(67890);
```
