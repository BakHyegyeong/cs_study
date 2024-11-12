# 단일 LinkedList와 이중 LinkedList의 차이는 무엇인가요?
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbpxqG0%2FbtrCqm679C3%2F8kFLWIcEsLvu8Kka72vKz1%2Fimg.png" width="70%">

- 단일 LinkedList
  - 각 노드는 다음 노드에 대한 포인터를 하나만 가지고 있음
    - 단방향으로만 탐색만 가능
    - 메모리 사용이 적음
  - 삽입과 삭제 시, 이전 노드를 추적해야 함
    - 삽입
        - head 노드 : O(1) - 추가하고 head로 변경
        - 중간 노드 : O(n) - 이전 노드를 찾기 위해 탐색
        - tail 노드 : 마지막 노드를 가리키는 포인터가 있을 경우 O(1), 마지막 노드를 가리키는 포인터가 없을 경우 O(n)
    - 삭제
        - head 노드 : O(1) - 추가하고 다음 노드를 head로 변경
        - 중간 노드 : O(n)
        - tail 노드 : 마지막 노드를 가리키는 포인터가 있든 없는 이전 노드 찾기 위해 탐색 O(n)

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FCohpq%2FbtrCrOBPLs2%2F8d1Vs1rFxF21Rtbqq98mp0%2Fimg.png" width="70%">

- 이중 LinkedList
  - 각 노드는 이전 노드와 다음 노드에 대한 포인터를 모두 가지고 있음. 
    - 양방향으로 탐색 가능
    - 두 개의 포인터로 인해 더 많은 메모리가 필요함
  - 이전 노드로의 이동이 쉬워 삭제와 삽입이 더 간단함
    - 인덱스를 알고 있다면 O(1)의 시간이 걸림
    - 삽입
        - head 노드 : O(1) - 추가하고 head로 변경
        - 중간 노드 : O(n)
        - tail 노드 :  마지막 노드를 가리키는 포인터가 있을 경우 O(1), 마지막 노드를 가리키는 포인터가 없을 경우 O(n)
  - 삭제 
    - head 노드 : O(1) - 추가하고 다음 노드를 head로 변경
    - 중간 노드 : O(n)
    - tail 노드 : 마지막 노드를 가리키는 포인터가 있을 경우 O(1), 마지막 노드를 가리키는 포인터가 없을 경우 O(n)

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbaTweJ%2FbtrCqGK19ud%2FZAFSrf5ErtkqxXL69MEF91%2Fimg.png">

- 원형 LinkedList
  - 마지막 노드의 next가 헤드 노드를 가리킴
  - 단일 원형 리스트와 이중 원형 리스트로 나뉨

# LinkedList의 삽입/삭제 성능은 언제 효율적이지 않을 수 있나요?
- 첫 노드에서 삽입/삭제 시 O(1)의 시간 복잡도를 가지지만, 중간 위치에서 삽입/삭제 시 해당 위치에 도달하기 위해 앞에서부터 순회해야 하므로 O(n)의 시간 복잡도 발생.
- 특정 노드를 찾기 위해 순회하는 경우에도 O(n)의 시간 복잡도 발생.
- 리스트의 중간 위치에서도 조작이 많이 일어나는 경우 LinkedList는 비효율적.
# ArrayList와 LinkedList는 각각 어떤 상황에서 선택하나요?
- ArrayList
  - 인덱스를 사용한 빠른 접근이 필요할 때(특정 인덱스의 요소에 빠르게 접근하고자 할 때)
  - 내부적으로 배열을 사용하므로 접근이 O(1)
  - 삽입시, 크기 조정으로 배열 복사가 일어나는 경우와 삭제시 뒤의 요소들을 이동시켜야 하므로 O(n)의 시간 복잡도를 가짐
  - 데이터가 고정적이거나 조회가 많은 경우 ArrayList를 선택
- LinkedList
  - 삽입과 삭제가 빈번히 발생하는 경우(특히 리스트의 양 끝에서 요소를 자주 삽입/삭제가 필요할 때)
  - 특정 노드에 접근을 위해서 순차적으로 탐색해야 하는 경우 O(n)의 시간 복잡도를 가짐
  - 데이터 크기가 가변적이거나 삽입/삭제가 빈번한 경우 LinkedList를 선택
