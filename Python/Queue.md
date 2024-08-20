## 특성

- FIFO(선입선출)
- 머리(Front)
    - 저장된 원소 중 첫 번째 원소(자료가 마지막으로 삭제된 위치)
- 꼬리(Rear)
    - 저장된 원소 중 마지막 원소(마지막 저장 위치)

## 연산

- enQueue(item)
    - 큐의 뒤쪽에 원소 삽입
- deQueue()
    - 큐의 앞쪽에 원소 삭제하고 반환
- createQueue()
    - 공백 상태 큐 생성
- isEmpty()
- isFull()
- Qpeek()
    - 큐 앞쪽에서 원소 삭제 없이 반환

---

- front = rear = -1
    - 큐가 비어 있는 상태

## 1. 선형큐

- 1차원 배열을 이용한 큐
- 큐 크기 = 배열의 크기
- 초기 상태: front = rear = -1
- 공백 상태: front == rear
- 포화 상태: rear == n-1(n: 배열의 크기, n-1: 배열의 마지막 인덱스)

```jsx
enqueue():
	rear += 1
	return Q[rear]
	
dequeue():
	front += 1
	return Q[front]
	
Qpeek():
	if isEmpty(): print('empty')
	else: return Q[front+1]
```

- 문제점
    - 잘못된 포화상태 인식
        - 앞 부분에 공간이 많은데 끝까지 front와 rear가 이동된 경우
- 해결방법
    - 매 연산이 이뤄질 때마다 원소들을 앞으로 이동
        - 큐 효율성이 급격히 떨어짐
    - 처음과 끝이 연결된 원형 큐라고 가정하고 사용

## 2. 원형큐

- front가 있는 자리는 사용하지 않고 빈 자리로 둠
- 값을 순회하면서 변경한 값을 선입선출 형식으로 구성하는 경우
    - 빈 공간을 하나 붙이고 그 공간에 값을 넣으면서 순회
    - rear가 front를 따라가는 형태로 앞에서부터 값 변경

```python
def create_password(numbers):
    password = numbers + [None]
    qsize = len(password)       # 크기 9

    front = 0
    rear = 8

    while password[rear] != 0:
        for i in range(1, 6):       # 1 ~ 5까지 빼기 (1 사이클)
            password[rear] = password[front] - i    # front 값에서 i를 빼고 rear에 넣기
            password[front] = None          # 디버깅용 - front 값을 뺏으면 None 할당
            if password[rear] <= 0:         # rear가 0이나 음수면 0으로 할당하고 반복문 종료
                password[rear] = 0
                break
            rear = (rear + 1) % qsize       # 인덱스는 다음 인덱스 값을 배열 크기로 나눈 나머지를 할당
            front = (front + 1) % qsize

    if front < rear:                # front가 None이므로 그 다음부터 끝까지
        return password[front+1:]
    else:                           # front가 None이므로 그 다음부터 끝까지, rear까지 포함해야하므로 rear+1까지
        return password[front+1:] + password[:rear+1]
```

|  | 삽입 위치 | 삭제 위치 |
| --- | --- | --- |
| 선형큐 | rear = rear+1 | front = front+1 |
| 원형큐 | rear = (rear+1) % n | front = (front+1) % n |

```python
cQ = [0] * Q_size
front = rear = 0

rear = (rear+1) % Q_size
cQ[rear]

front = (front+1) % Q_size
print(cQ[front])
```

## 3. 덱(deque)

```python
from collections import deque

q = deque()
q.append
t = q.popleft()    # 왼쪽에서 요소 제거하고 반환, 없으면 indexError
```

## 4. 우선순위큐

- 배열을 이용 or 리스트 이용
- 배열 이용
    - 원소 삽입 과정에서 우선순위를 비교해 적절한 위치에 삽입
    - 최우선순위가 앞에 위치
    - 문제점
        - 삽입, 삭제할 때 원소 재배치
        - 시간, 메모리 낭비 큼

## 5. 버퍼

- 데이터를 한 곳에서 다른 한 곳으로 전송하는 동안 일시적으로 그 데이터를 보관하는 영역
- 버퍼링
    - 버퍼를 활용하는 방식 or 버퍼를 채우는 동작
- 입출력 및 네트워크 관련된 기능에서 주로 사용
- 선입선출 방식인 큐를 사용

## 6. BFS(너비 우선 탐색)

- 시작점의 인접한 정점들을 모두 방문한 후 방문했던 정점을 다시 시작점으로

### 과정

1. Q생성, visited 초기화, 시작점 enqueue
2. 시작점과 인접점들을 enqueue
3. 그 다음 단계의 인점접들을 enqueue

```python
1. BFS(너비 우선 탐색) 개념:
   - BFS는 그래프나 트리를 탐색하는 알고리즘
   - 시작 노드에서 가까운 노드부터 차례대로 탐색
   - 같은 레벨의 모든 노드를 탐색한 후 다음 레벨로 넘어감

2. 큐(Queue) 사용:
   - BFS는 큐 자료구조를 사용하여 구현
   - 큐는 선입선출(FIFO) 방식으로 동작
   - 탐색할 노드를 큐에 넣고, 순서대로 꺼내며 탐색

3. 방문 체크:
   - 이미 방문한 노드를 다시 방문하지 않도록 방문 여부를 체크
   - `visited` 리스트를 사용하여 각 노드의 방문 상태를 관리

4. 인접 노드 탐색:
   - 현재 노드와 연결된 모든 인접 노드를 확인
   - 방문하지 않은 인접 노드를 큐에 추가

5. 그래프 표현:
   - 이 코드에서는 인접 행렬을 사용하여 그래프를 표현
   - `G[i][j] = 1`은 노드 i와 j가 연결되어 있음을 의미

6. 탐색 순서:
   - 시작 노드부터 가까운 순서대로 노드를 탐색
   - 결과적으로 시작 노드로부터의 거리 순으로 노드를 방문

7. 구현 과정:
   - 시작 노드를 큐에 넣고 방문 표시를 
   - 큐가 빌 때까지 다음 과정을 반복:
     a) 큐에서 노드를 하나 꺼낸다.
     b) 꺼낸 노드의 인접 노드 중 방문하지 않은 노드를 모두 큐에 넣고 방문 표시를 한다.
```

```python
# 노드 수(V)와 간선 수(E) 입력
V, E = map(int, input().split())

# 간선 정보 입력
edge_info = list(map(int, input().split()))

# 그래프 초기화 (인접 행렬)
G = [[0] * (V + 1) for _ in range(V + 1)]

# 방문 여부를 체크할 리스트 초기화
visited = [0 for _ in range(V + 1)]

# 간선 정보를 바탕으로 그래프(인접 행렬) 구성
for i in range(0, len(edge_info), 2):
    n1, n2 = edge_info[i], edge_info[i + 1]
    G[n1][n2] = G[n2][n1] = 1  # 무방향 그래프이므로 양방향 연결

def BFS(start):
    queue = [start]  # 탐색할 노드를 저장하는 큐
    print(start, end=' ')  # 시작 노드 출력
    visited[start] = 1  # 시작 노드 방문 표시

    while queue:  # 큐가 비어있지 않은 동안 반복
        current = queue.pop(0)  # 큐에서 노드를 하나 꺼냄
        for next_node in range(1, V + 1):  # 모든 노드에 대해
            # 현재 노드와 연결되어 있고, 아직 방문하지 않은 노드라면
            if G[current][next_node] == 1 and visited[next_node] == 0:
                queue.append(next_node)  # 큐에 추가
                visited[next_node] = 1  # 방문 표시
                print(next_node, end=' ')  # 노드 출력

# 그래프 확인
# pprint(G)

# BFS 실행 (시작 노드: 1)
BFS(1)
```

- visited[next_node] 값을 visited[current_node] += 1으로 설정하면 최단거리, 최단시간 구할 수 있음
    - ex. visited에 기록할 때 단계의 숫자를 기록하면 해당 노드가 몇 번째 단계에 있는지 알 수 있고 이는 해당 경로까지 간선의 갯수 == 최단 거리