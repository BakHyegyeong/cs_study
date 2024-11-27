# Call by Value와 Call by Reference의 차이

변수나 객체등이 함수의 인자로 들어와 매개변수로 전달될 때 어떤 방식으로 전달될 지를 결정하는 방식

→ 값을 **복사**를 해서 처리하냐, 아니면 **직접 참조**를 하느냐의 차이이다.

일반적으로 **원시형(원시값의 data type)**을 매개변수로 넘길 때에는

**call by value** 방식으로 넘기게 되고,

**참조형(객체)** 을 매개변수로 넘길 때에는

**call by reference** 방식으로 넘기게 된다.


> ⭐ - 원시값 : Number, String, BigInt, Symbol, Boolean, Null, undefined <br> - 참조값 : 객체, 배열, 함수, 날짜, 정규표현식 등등

## Call by Value

함수가 인자로 **전달받은 값을 복사하여 처리하는 방식**이다.

![](https://user-images.githubusercontent.com/41438361/87274841-b03e1600-c517-11ea-804d-861255b66975.png)

- 장점 : 복사하여 처리하기 때문에 안전하다. 원래의 값이 보존이 된다.
- 단점 : 복사를 하기 때문에 메모리가 사용량이 늘어난다.

## Call by Reference

**실제 데이터가 존재하는 주소**를 가리키는 주소값을 인자로 넘겨 매개변수로 전달한다.

→  인자로 **전달된 변수의 값을 변경**하면, **호출한 쪽에서도 해당 변수의 값이 변경**된다. 

![](https://user-images.githubusercontent.com/41438361/87274967-0b700880-c518-11ea-939d-e41a35ddf1f4.png)

- 장점 : 복사하지 않고 직접 참조를 하므로 빠르다.
- 단점 : 직접 참조를 하기 때문에 원래 값이 영향을 받는다.

## Call by Reference 단점의 보완

원본 객체의 불변성을 지킬 수 없다는 단점이 있다.

→ 이 문제점을 해결하기 위해 **깊은 복사**를 이용하는 방법이 있다.

```java
function changeName(obj) {
  obj = JSON.parse(JSON.stringify(obj)); // 깊은 복사
  obj.name = 'John';
  return obj;
}

let person = { name: 'Jane', age: 25 };
let result = changeName(person);
console.log(result); // 출력: { name: 'John', age: 25 }
console.log(person); // 출력: { name: 'Jane', age: 25 } (원본 값은 변경되지 않음)
```

깊은 복사를 사용하면 객체를 복사했을 때 원본에 영향을 주지 않는다.

다만 단점의 보완을 위해 복사를 사용하게 되면 **메모리를 소모하게 되고, 속도의 장점을 잃어버릴 수 있다.**


---

# 배열과 연결 리스트의 차이

## Array

- 탐색 : O(1)
- 삽입/삭제 : O(n)
- **정적** 자료구조
- 초기화시 크기가 고정된다.
    
    → 해당 크기 만큼의 **연속된 메모리 주소**를 할당 받게 된다.
    
    → **크기를 수정하는 것이 불가능**해 배열 크기 이상의 데이터를 저장할 수 없다.
    
- 인덱스를 통한 **임의 접근이 가능**하다.
    
    → 접근과 탐색에 용이하다.
    

```java
int[] numbers = new int[5];
```

## LinkedList

: 데이터를 감싼 노드를 **포인터로 연결**해서 공간적인 효율성을 극대화시킨 자료 구조


![](https://github.com/inflearn-cs-study/cs/raw/main/Data%20Structures/DS_02/Bakhyegyeong_image/image5.png)


- 탐색 : O(n)
- 삽입 : O(1)
- 동적 자료구조
- 새 노드를 동적으로 할당하거나 제거함으로써 **리스트의 크기를 변경**할 수 있다.
- 원소에 접근하기 위해 **리스트를 순차적으로 탐색**해야 한다.
    
    → 임의로 접근하는 것이 불가능하다.
    
- 원소를 삽입하거나 삭제할 때, **포인터를 업데이트만 하면 되므로 상대적으로 빠른 시간**에 처리할 수 있다.

## Array와 LinkedList의 차이점

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcLMZMC%2Fbtr7eWXfals%2Ff8N3JK2t7RBTHivsL1bNTK%2Fimg.png)

| **배열(Array)** | **연결 리스트(Linked List)** |
| --- | --- |
| 정적 자료구조 | 동적 자료구조 |
| 미리 크기를 정해 놓음 | 크기를 정할 필요가 없음 |
| 연속된 메모리 주소를 할당 받음 | 연속된 메모리 주소를 할당 받지 않음 |
| 접근, 탐색 용이 | 추가, 삭제 용이 |
| index 존재 | Node 존재 |

## Array와 LinkedList 사용처

- Array : 인덱스를 사용한 **빠른 접근**이 필요하거나 **크기가 고정**된 경우에 적합
- LinkedList : 데이터의 **삽입과 삭제**가 빈번하게 발생하거나 **크기가 가변적인 경우**에 적합
