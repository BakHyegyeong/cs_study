## **Array와 ArrayList의 공통점과 차이점**

### 공통점

1. Array와 ArrayList는 요소를 추가(resize가 발생하지 않는 추가)하거나 가져올 때의 성능이 비슷하다.
2. 중복 요소 저장 가능
3. null값을 저장할 수 있고 index를 이용해 값을 참조할 수 있다.

### 차이점

1. 크기
    1. Array : **초기화시 고정**된다.
    2. ArrayList : 초기화시 사이즈를 표시하지 않고 **크기가 가변적**이다.
2. 속도
    1. Array : 초기화시 메모리에 할당되어 ArrayList보다 속도가 빠르다.
    2. ArrayList : 데이터 추가, 삭제시 메모리를 재할당하기 때문에 Array보다 속도가 느리다.
3. 다차원
    1. Array : 가능하다.
    2. ArrayList : 불가능하다.
4. 저장하는 변수
    1. Array : primitive data type(int, float, double 등), Object를 저장할 수 있다.
    2. ArrayList : primitive data type을 저장할 수 없고 Object만 저장할 수 있다.
5. 순회 방법
    1. Array : for, for each 등을 통해 순회할 수 있다.
    2. ArrayList : iterator를 통해 순회할 수 있다.

## **ArrayList에서 크기를 동적으로 조정하는 방식은 무엇인가요?**

: ArrayList가 생성될 때 **Default로 10개의 공간을 가진 배열**로 시작한다.

하지만 **최적화(지연 초기화)로 인해 생성될 당시에는 0의 크기로 시작**되는데 
**첫 add()가 발생할 경우 resize를 통해 배열의 크기가 10으로 조정**된다.

이후 ArrayList에 10개의 데이터가 있는 상태에서 데이터를 add()할 때 
다시 resize가 발생하고 크기가 1.5배 증가하는데

이는 **크기가 1.5배 증가한 배열에 기존의 배열을 copy하는 과정**으로 이루어진다.

## **Array와 ArrayList는 각각 어떤 상황에서 더 적합한가요?**

: 값의 개수가 고정적이고 단순할 때와 메모리 효율이 중요한 경우에는 Array를 사용하며

값의 개수가 유동적이고 계속 추가되고 편의성이 중요한 경우에는 ArrayList를 사용한다.