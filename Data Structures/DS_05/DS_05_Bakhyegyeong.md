## **이진 탐색 트리 (BST, Binary Search Tree)**

: **이진 탐색(binary search)의 효율적인 탐색 방식**을 사용하면서, **연결 리스트(linked list)로 삽입, 삭제를 용이**하게 만든 자료 구조

- **노드의 왼쪽 하위 트리**에는 **노드의 키보다 작은 키**가있는 노드 만 포함됩니다
- **노드의 오른쪽 하위 트리**에는 **노드의 키보다 큰 키**가있는 노드 만 포함됩니다.
- 왼쪽 및 오른쪽 하위 트리도 각각 이진 검색 트리 여야합니다.
- **중복된 키를 허용하지 않습니다.**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbCe3QD%2Fbtq2ytHuN1Z%2FAi82KHYBlgY01j9hbwjOO1%2Fimg.png)

### 이진 탐색 트리의 생성

**50, 15, 62, 80, 7, 54, 11**

1. **50을 트리의 루트**로 트리에 삽입
2. 다음 요소를 읽고 루트 노드 요소보다 **작으면 왼쪽** 하위 트리의 루트로 삽입
3. 루트 노드 요소보다 **크다면 오른쪽** 하위 트리의 루트로 삽입

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcexmJD%2Fbtq2z1DLANG%2FZFiFmM5657r46N4hKKzv91%2Fimg.png)

### 이진 탐색 트리의 장단점

**장점** 

- 삽입, 삭제가 유연하다.
    
    → 삭제된 노드를 가리키고 있던 reference만 조정 해주면 된다.
    
- 값의 크기에 따라 좌/우 서브트리가 나눠지기 때문에 삽입 / 삭제 / 검색이 일반적으로 빠르다 O(logN).
- 값의 순서대로 순회가 가능하다.

**단점**

보통 요소를 찾을 때 이진 탐색 트리의 경우 O(logn)이 소모되지만 최악의 경우 선형적 트리, 리스트가 되면서 **O(n)** 이 소모된다.

![](https://velog.velcdn.com/images/rik963/post/eb771817-1ccf-45f0-83fc-00a095dcfee7/image.png)

## 트리 순회

: 트리에 속하는 모든 노드를 체계적으로 하나씩 방문하여 노드의 데이터를 목적에 맞게 처리하는 것

- 전위 순회 (preorder traversal) : **루트** → 왼쪽 → 오른쪽
    
    → 트리 복사에 활용
    
- 중위 순회 (inorder traversal) : 왼쪽 →  **루트** → 오른쪽
    
    → 오름차순 또는 내림차순으로 값을 가져올 때 활용
    
- 후위 순회 (postorder traversal) : 왼쪽 → 오른쪽 → **루트**
    
    → 트리 삭제에 활용, 부모 노드 삭제 전 자식 노드를 삭제
    

### 전위 순회

```java
public void preOrder(Node node) {
	if(node != null) {
		System.out.print(node.data + " ");
		if(node.left != null) preOrder(node.left);
		if(node.right != null) preOrder(node.right);
	}
}
```

![](https://www.jiwon.me/content/images/size/w1000/2021/11/preorder.png)

### 중위 순회

```java
public void inOrder(Node node) {
	if(node != null) {
		if(node.left != null) inOrder(node.left);
		System.out.print(node.data + " ");
		if(node.right != null) inOrder(node.right);
	}
}
```

![](https://www.jiwon.me/content/images/size/w1000/2021/11/inorder.png)

### 후위 순회

```java
public void postOrder(Node node) {
	if(node != null) {
		if(node.left != null) postOrder(node.left);
		if(node.right != null) postOrder(node.right);
		System.out.print(node.data + " ");
	}
}
```

![](https://www.jiwon.me/content/images/size/w1000/2021/11/postorder.png)

## 힙

: 완전 이진 트리 기반의 자료 구조이다.

- 최대 힙 : 루트 노드에 있는 키가 최댓값
- 최소 힙 : 루트 노드에 있는 키가 최솟값

![](https://velog.velcdn.com/images/yyj8771/post/13008f33-c966-4ca4-80dd-7095077c3e8e/image.png)

### 최대 힙의 삽입

1. 새로운 노드를 힙의 마지막 노드에 삽입한다.
2. 부모 노드들과의 크기를 비교하며 교환한다.

![](https://velog.velcdn.com/post-images%2Fholicme7%2F80df2f10-20ae-11ea-98a2-7355d7fc70cd%2Fmaxheap-insertion.png)

### 최대 힙의 삭제

1. 루트 노드를 삭제한다.
2. 삭제된 루트 노드에 마지막 노드를 가져온다.
3. 자식 노드와 크기를 비교하며 교환한다.

![](https://velog.velcdn.com/post-images%2Fholicme7%2F6e807760-20af-11ea-b8a5-09eea756748a%2Fmaxheap-delete.png)

## 우선순위 큐

: 우선순위 대기열이라고도 하며 우선순위가 높은 요소가 우선순위가 낮은 요소보다 먼저 제공되는 자료구조

### 배열로 구현

: 데이터의 우선순위가 높을수록 배열의 앞쪽에 데이터를 위치시켜서 구현

→ 배열로 구현 시 시간 복잡도 : **삭제는 O(1), 삽입은 O(n)**

- 배열의 특성 상 데이터를 삽입, 삭제하는 과정에서 **데이터를 한 칸씩 뒤로 밀거나 앞으로 당기는 연산을 수행해야 하기 때문에 비효율적**
- 최악의 경우 삽입해야 하는 위치를 찾기 위해 **모든 인덱스를 탐색해 우선순위의 비교**해야 한다.

### 연결 리스트로 구현

: 데이터의 우선순위가 높은 순서대로 연결

→ 연결리스트로 구현 시 시간 복잡도 : **삭제는 O(1), 삽입은 O(n)**

- 삽입 위치를 찾기 위해 **배열에 저장된 모든 데이터와 삽입하려는 데이터의 우선순위를 비교**해야 할 수도 있다는 단점

### 힙으로 구현

: **루트 노드에 우선순위가 가장 높은 데이터를 위치**시킬 수 있기 때문에 우선순위 큐를 구현할 수 있는 자료구조이다.

→ 힙으로 구현 시 시간 복잡도 : **삭제는 O(log2n), 삽입은 O(log2n)**

더하여 **삭제나 삽입 과정에서 부모와 자식간의 비교**만 이루어지면서 삭제나 삽입 모두 최악의 경우 O(log2n)의 시간 복잡도를 가진다.

이는 배열이나 연결 리스트에 비해서 삭제에서는 부족하지만 삽입이 월등하게 효율이 좋으므로 **편차가 심한 배열과 연결리스트보다는 힙으로 구현**한다.
