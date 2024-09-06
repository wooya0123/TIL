## 1. 특징

- 비선형 구조
    - 원소들 간 1:n 관계의 자료 구조
    - 원소들 간 계층 관계, 하위로 갈수록 확장
- 노드: 트리의 원소
    - 형제 노드: 같은 부모 노드의 자식 노드들
    - 조상 노드: 간선을 따라 루트 노드까지 경로에 있는 모든 노드들
    - 자손 노드: subtree에 있는 하위 레벨 노드들
- 간선: 노드를 연결하는 선, 부모 - 자식을 연결
- 루트: 최상위 노드
    - 나머지 노드들은 하나의 트리가 될 수 있음 (= subtree)
    - leaf 노드: 차수가 0, 자식이 없는 노드 (=단말노드)
- 차수: 노드에 연결된 자식 노드의 수
    - 트리의 차수: 트리에 있는 노드의 차수 중 가장 큰 값
- 높이: 루트 - 노드 간선의 수
    - 루트의 높이 = 0
    - 트리의 최대 높이 = $log N$
    - 계층 구조기 때문에 형제 노드끼리 연결 불가(=사이클이 없다)
    - 시간복잡도를 바로 계산 가능

## 2. 이진 트리

- 모든 노드들이 최대 2개의 서브트리를 갖는 특별한 형태의 트리
- 레벨 i에서 노드의 최대 개수는 $2^i$
    - 높이가 h인 경우 노드의 최소 개수는 h+1, 최대 개수는 $2^{h+1}-1$
- 1차원 배열보다 연결 리스트 사용하는 것이 좋음 (데이터 삽입, 삭제가 쉽고 리스트 크기 고정)
    

### 포화 이진 트리

- 모든 레벨의 노드가 포화 상태
    - 높이가 h일 때 최대 노드 $2^{h+1}-1$의 노드를 가짐
    - 루트를 1번으로 하고  $2^{h+1}-1$까지 정해진 위치에 대한 노드 번호


### 완전 이진 트리

- 높이가 h, 노드 수가 n ($2^h$≤ n ≤ $2^{h+1}-1$)
    - 노드 번호 1번부터 n번까지 빈 자리가 없는 이진 트리
    
    
- 이진 트리 → 완전 이진 트리(빈 자리가 없으면) → 포화 이진 트리(빈 자리 없고 꽉 차 있으면)

## 3. 이진 트리 순회

- 순회: 각 노드를 중복되지 않게 전부 방문
    - 비선형이기 때문에 선후 관계를 알 수 없음 → 아래 방법 필요

### 전위 순회(자신 - 왼 - 오)

- 방법
1. 현재 노드 방문 처리
2. 현재 노드 기준 왼쪽 서브 트리로 이동, 방문 처리
    1. 왼쪽에 노드가 있으면 왼쪽 먼저
3. 더 이상 왼쪽에 노드가 없으면 오른쪽 보고 상위 노드로 올라감


### 중위 순회 (왼 - 자신 - 오)

- 방법
1. 현재 노드 기준 왼쪽 서브 트리로 이동
2. 더 이상 왼쪽에 노드가 없으면 현재 노드 방문 처리 후 상위로 올라가서 방문 처리
3. 오른쪽 노드 방문 처리 후 상위 노드로

    <details>
      <summary>중위 순회 방법</summary>

    ```python
    def in_order(T):
        result = ''
        if T:
            result += in_order(left[T])     # 왼쪽 먼저 탐색
            result += word[T]               # 더 이상 왼쪽에 값이 없으면 해당 노드의 단어를 result에 넣고 오른쪽 보기
            result += in_order(right[T])    # 오른쪽 노드로 이동해서 없으면 0이면 그냥 반환 -> 지금까지 result 가지고 부모 노드로, 있으면 해당 노드의 왼쪽부터 다시 보기

        return result
        
    left = [0]*(N+1)        # 부모를 인덱스로 왼쪽자식번호 저장
    right = [0]*(N+1)       #
    par = [0]*(N+1)         # 자식을 인덱스로 부모 저장

    for i in range(E):
        p, c = arr[i*2], arr[i*2+1]
    # for i in range(0,E*2, 2):
    #         p, c = arr[i], arr[i + 1]
        if left[p]==0:          # 왼쪽자식이 없으면
            left[p] = c
        else:
            right[p] = c
        par[c] = p
    
    c = N
    while par[c]!=0:        # 부모가 있으면
        c = par[c]          # 부모를 새로운 자식으로 두고
    root = c                # 더이상 부모가 없으면 root
    print(root)
    pre_order(root)
    ```

    </details>
    <details>
      <summary>연결 리스트 활용</summary>

    ```python
    def in_order(T):
        value = node[T][0]
        left = node[T][1]
        right = node[T][2]

        if T:
            in_order(left)
            print(value, end='')
            in_order(right)

    T = 10

    for tc in range(1, T+1):
        N = int(input())    # 트리가 갖는 노드의 수

        node = [[0, 0, 0] for _ in range(N+1)]    # idx: 노드 번호, 0: 노드 값, 1: 왼쪽, 2: 오른쪽

        for _ in range(N):
            data = input().split()
            if len(data) == 4:
                n, v, l, r = data
                n, l, r = int(n), int(l), int(r)
                node[n][0] = v
                node[n][1] = l
                node[n][2] = r
            elif len(data) == 3:
                n, v, l = data
                n, l = int(n), int(l)
                node[n][0] = v
                node[n][1] = l
            else:
                n, v = data
                n = int(n)
                node[n][0] = v

        print(f'#{tc} ', end='')
        in_order(1)
        print()
    ```

    </details>
    <details>
      <summary>중위 순회 방법으로 1~N 까지 자연수 배열</summary>

    ```python
    def in_order(T, count):
        if T <= N:
            count = in_order(2*T, count)
            tree[T] = count
            count += 1
            count = in_order(2*T+1, count)
        return count

    T = int(input())

    for tc in range(1, T+1):
        N = int(input())

        tree = [0] * (N + 1)
        in_order(1, 1)
    ```

    </details>
    <details>
      <summary>class로 구현</summary>

    ```python
    from collections import deque

    class TreeNode:
        def __init__(self, key):
            self.key = key  # 노드의 값
            self.left = None  # 왼쪽 자식 노드를 가리킴
            self.right = None  # 오른쪽 자식 노드를 가리킴

    class BinaryTree:
        def __init__(self):
            self.root = None  # 트리의 루트 노드

        # 새로운 노드를 삽입하는 함수 (레벨 순서 삽입)
        def insert(self, key):
            new_node = TreeNode(key)
            if self.root is None:
                self.root = new_node
                return

            # 레벨 순서로 트리를 탐색하기 위해 큐를 사용
            queue = deque([self.root])

            while queue:
                node = queue.popleft()

                # 왼쪽 자식이 비어있으면 삽입
                if node.left is None:
                    node.left = new_node
                    break
                else:
                    queue.append(node.left)

                # 오른쪽 자식이 비어있으면 삽입
                if node.right is None:
                    node.right = new_node
                    break
                else:
                    queue.append(node.right)

        def inorder_traversal(self):
            # 중위 순회를 통해 트리의 노드들을 출력하는 함수
            return self._inorder_traversal(self.root, [])

        def _inorder_traversal(self, node, result):
            if node:
                self._inorder_traversal(node.left, result)
                result.append(node.key)
                self._inorder_traversal(node.right, result)
            return result

    # 예제 사용법
    if __name__ == "__main__":
        tree = BinaryTree()
        tree.insert(50)
        print("Inorder Traversal:", tree.inorder_traversal())
        tree.insert(30)
        print("Inorder Traversal:", tree.inorder_traversal())
        tree.insert(20)
        print("Inorder Traversal:", tree.inorder_traversal())
        tree.insert(40)
        print("Inorder Traversal:", tree.inorder_traversal())
        tree.insert(70)
        print("Inorder Traversal:", tree.inorder_traversal())
        tree.insert(60)
        print("Inorder Traversal:", tree.inorder_traversal())
        tree.insert(80)

        print("Inorder Traversal:", tree.inorder_traversal())
    ```

    </details>

### 후위 순회 (왼 - 오 - 자신)

1. 현재 노드 기준 왼쪽 서브 트리로 이동
2. 더 이상 자식 노드가 없으면 현재 노드 방문 처리
3. 해당 부모 노드의 나머지 자식 노드 방문 처리 후 → 부모 노드 방문 처리

## 4. 이진 트리 표현

- 높이에 따라 노드 번호를 예측 가능함 (루트가 1번일 경우)


- 노드 번호가 i의 왼쪽, 오른쪽 자식 노드 번호
    - $2 * i$, $2*i+1$
- 레벨 n의 노드 시작 번호
    - $2^n$

### 배열을 이용한 표현

- 노드 번호를 인덱스로 사용
    - 잘 쓰지 않음 - 메모리 낭비가 심함
- left, right 리스트
    - 부모 노드 번호를 인덱스로 left, right 노드에 노드 번호를 기록
- par 리스트
    - 자식 번호 노드를 인덱스로 부모 노드 번호를 저장

### 연결 리스트 표현 vs 인접 리스트 표현

- 실제 개발 시에 사용 vs 코테에 사용

## 5. 이진 탐색 트리(BST)

- 특정 규칙을 갖도록 데이터를 체계적으로 저장, 최대 O($log N$)의 빠른 속도로 값을 검색
    - 부모 노드보다 작으면 왼쪽, 크면 오른쪽

### 리스트 vs BST

- 리스트 성능
    - 삽입 O(n), 맨 끝 삽입은 O(1)
        - 중간에 삽입하면 그 뒤를 한 칸씩 밀어야 해서
    - 삭제 O(n), 맨 끝 삭제는 O(1)
    - 탐색 O(n)
        - ex. if target in arr  →  리스트의 처음부터 끝까지 탐색하기 때문에 시간 초과될 수 있음
- BST 성능
    - 삽입, 삭제, 탐색 평균 O($log N$) - 트리 높이 만큼 시간이 걸림
    - 만약 데이터가 편향되게 들어오면 리스트와 비슷한 시간(O(n)) 걸림

---

- key 값이 작은 순서대로 탐색 ⇒ 오름차순 정렬도 됨
- 삭제 연산
    1. 리프 노드는 그냥 삭제해도 문제 없음
    2. 삭제하려는 노드가 자식이 하나일 때: 자식 - 부모 연결 후 삭제
    3. 삭제하려는 노드가 자식이 둘일 때: 
        1. 반을 갈라서 왼쪽 서브트리의 가장 큰 값 or 오른쪽 서브 트리의 가장 작은 값을 넣기
        2. 위 값을 찾아서 끝까지 내려가면 리프 노드가 됨
    
    <details>
      <summary>이진 탐색 트리 클래스 구현</summary>

    ```python
    class Node:
        def __init__(self, key):
            self.key = key
            self.left = None
            self.right = None

    class BinarySearchTree:
        def __init__(self):
            self.root = None

        def insert(self, key):
            if self.root is None:
                self.root = Node(key)
            else:
                self._insert(self.root, key)

        def _insert(self, node, key):       # node: 현재 바라보는 노드, key: 삽입하고자 하는 데이터
            if key < node.key:              # 작으면 왼쪽 고려
                if node.left is None:       # 왼쪽에 삽입 가능 -> 그냥 삽입
                    node.left = Node(key)
                else:
                    self._insert(node.left, key)    # 왼쪽에 데이터가 있으면 재귀로 한 번 더 탐색
            else:
                if node.right is None:      # 오른쪽에 삽입 가능 -> 그냥 삽입
                    node.right = Node(key)
                else:
                    self._insert(node.right, key)

        def search(self, key):
            return self._search(self.root, key)

        def _search(self, node, key):               # node: 현재 바라보는 노드, key: 삽입하고자 하는 데이터
            if node is None or node.key == key:     # 타겟과 같으면 return
                return node
            if key < node.key:                          # 타겟이 노드보다 작으면
                return self._search(node.left, key)     # 노드의 왼쪽 값을 넣어서 다시 탐색
            else:                                       # 타겟이 노드보다 크면
                return self._search(node.right, key)    # 노드의 오른쪽 값을 넣어서 다시 탐색
        def inorder_traversal(self):
            self._inorder_traversal(self.root)

        def _inorder_traversal(self, node):
            if node:
                self._inorder_traversal(node.left)
                print(node.key, end=' ')
                self._inorder_traversal(node.right)
    ```

    </details>

## 6. 힙(heap)

- 완전 이진 트리에 있는 노드 중 키 값이 가장 큰 노드 or 키 값이 가장 작은 노드를 찾는 자료구조
- 주의 사항
    - 부모 - 자식 간 규칙은 유지되지만 형제끼리는 규칙 없음
    - 특정값을 탐색에는 적합하지 않음
- 삽입
    - 한 번 연산: O(log N)
    - 전체 연산: O(N * log N)
    - 맨 마지막에 데이터 삽입 → 부모 노드와 비교하면서 자리 변경
- 삭제
    - O(log N)
    - 루트 노드의 원소만 삭제
        - 루트 노드 삭제 → 마지막 노드로 대체 → 자식 노드 중 더 큰 것과 교체(max heap의 경우)
- max heap
    - 부모 노드 키 값 < 자식 노드 키 값
    - 루트 노드: 키 값이 가장 큰 노드

        <details>
          <summary>코드 구현</summary>

        ```python
        # 최대힙
        def enq(n):
            global last
            last += 1   # 마지막 노드 추가(완전이진트리 유지)
            h[last] = n # 마지막 노드에 데이터 삽입
            c = last    # 부모>자식 비교를 위해
            p = c//2    # 부모번호 계산
            while p >= 1 and h[p] < h[c]:   # 부모가 있는데, 더 작으면
                h[p], h[c] = h[c], h[p]  # 교환
                c = p
                p = c//2

        def deq():
            global last
            tmp = h[1]   # 루트의 키값 보관
            h[1] = h[last]
            last -= 1
            p = 1           # 새로 옮긴 루트
            c = p*2
            while c <= last:  # 자식이 있으면
                if c+1 <= last and h[c] < h[c+1]: # 왼쪽 오른쪽 비교해서 더 큰 값 찾기
                    c += 1
                if h[p] < h[c]:
                    h[p], h[c] = h[c], h[p]
                    p = c
                    c = p*2
                else:
                    break
            return tmp

        N = int(input())          # 필요한 노드 수
        arr = list(map(int, input().split()))

        h = [0]*(N+1)   # 최대힙
        last = 0        # 힙의 마지막 노드 번호

        for num in arr:
            enq(num)

        print(h)

        while last > 0:
            print(deq(), end=' ')
        ```

        </details>
        <details>
          <summary>라이브러리 활용</summary>

        ```python
        from heapq import heappush, heappop

        N = int(input())          # 필요한 노드 수
        arr = list(map(int, input().split()))

        heap = []  # 최대힙을 구현하기 위한 리스트

        # 최소힙 ( 기본값이 최소힙 )
        for num in arr:
            heappush(heap, num)

        print([x for x in heap])  # 힙의 내부 상태를 출력 (음수로 저장된 상태)

        while heap:
            print(heappop(heap), end=' ')

        print('\n------------------------------------')

        # 최대힙
        # 삽입 시 음수로 곱하여 저장 (제일 큰 수가 제일 작아짐)
        # 삭제 후 음수값을 곱하여 출력 (다시 원래 수로 복구하여 출력)
        for num in arr:
            heappush(heap, -num)

        print([-x for x in heap])  # 힙의 내부 상태를 출력 (음수로 저장된 상태)

        while heap:
            print(-heappop(heap), end=' ')
        ```

        </details>

- min heap
    - 부모 노드 키 값 > 자식 노드 키 값
    - 루트 노드: 키 값이 가장 작은 노드

        <details>
          <summary>코드 구현</summary>

        ```python
        # 최소힙
        def enq(n):
            global last
            last += 1   # 마지막 노드 추가(완전이진트리 유지)
            h[last] = n # 마지막 노드에 데이터 삽입
            c = last    # 부모>자식 비교를 위해
            p = c//2    # 부모번호 계산
            while p >= 1 and h[p] > h[c]:   # 부모가 있는데, 더 크면
                h[p], h[c] = h[c], h[p]  # 교환
                c = p
                p = c//2

        def deq():
            global last
            tmp = h[1]   # 루트의 키값 보관
            h[1] = h[last]
            last -= 1
            p = 1           # 새로 옮긴 루트
            c = p*2
            while c <= last:  # 자식이 있으면
                if c+1 <= last and h[c] > h[c+1]:  # 오른쪽자식이 있고 더 작으면
                    c += 1
                if h[p] > h[c]:
                    h[p], h[c] = h[c], h[p]
                    p = c
                    c = p*2
                else:
                    break
            return tmp

        N = int(input())          # 필요한 노드 수
        arr = list(map(int, input().split()))

        h = [0]*(N+1)   # 최대힙
        last = 0        # 힙의 마지막 노드 번호

        for num in arr:
            enq(num)

        print(h)

        while last > 0:
            print(deq(), end=' ')

        ```

        </details>

