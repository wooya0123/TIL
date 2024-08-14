## 1. 특성

- 선형 구조
    - 자료 간 관계가 1:1의 관계
    - (비선형 구조: 자료 간 관계가 1:N의 관계)
- 자료 삽입, 꺼내기 가능
- 마지막에 삽입한 자료를 가장 먼저 꺼냄 (후입선출 LIFO)
- 선형으로 저장할 저장소(배열 사용 가능)
- 저장소 자체를 스택이라 부르기도 함
- top : 마지막으로 원소가 저장된 위치

## 2. 연산

### push

- 자료 삽입
- append 사용
    - append 작동 구조가 적당히 큰 리스트를 만들고 자료 넣는데 부족하면 더 큰 리스트 만들고 이전 리스트 복사

```jsx
def push(item, size):
	global top
	top += 1
	if top == size:
		print('overflow')
	else:
		stack[top] = item

```

### pop

- 자료 꺼내기
- pop 사용 (맨 뒤 꺼내기, pop(0) → 앞에 거 꺼내기)

```jsx
def my_pop():
	global top
	if top == -1
		print('underflow')
		return 0
	else:
		top -= 1
		return stack[top+1]
```

- pop하고 그 자리를 삭제하지 않아도 됨
    - 이후 push하게 되면 그 자리에 덮어씀

- 공백인지 아닌지 확인(.isEmpty)
- top에 있는 원소 반환(.peek)

## 3. Function call

- main은 시스템 자체의 메모리 영역
- 함수 안에 함수는 작동하고 나면 값을 반환하고 pop (stackpoint가 내려감)
    - 그 자리는 push할 경우 다른 것으로 채워질 수 있음

## 4. 재귀호출

- 필요한 함수가 자신과 같은 경우 자신을 다시 호출하는 구조
- 일반 호출방식보다 프로그램 크기 줄이고 간단하게 작성 가능
- ex. 피보나치 수열

```
def fibo(n):
	if n < 2:
		return n
	else:
		return fibo(n-2) + fibo(n-1)
```

- 모든 배열 원소에 접근하기
    - 최소 2개의 매개변수 갖는 것이 일반적

```
def f(i, N):       # i는 현재 인덱스, N 크기
	if i == N:       # 배열을 벗어난 경우
		return
	else:
		print(arr[i])
		f(i+1, N)
		return

arr = [1, 2, 3]
N = 3
f(0, 3)
```

## 5. Memoization

- 이전에 계산한 값을 메모리에 저장해서 매번 다시 계산하지 않도록 하여 실행속도 빠르게
- 복잡도 2**n 을 n으로 줄일 수 있음
- 실행방법
    1. memo를 위한 배열을 할당하고 모두 0으로 초기화
    2. memo[0]을 0으로 memo[1]은 1로 초기화
    
    ```
    def fibo1(n):
    	if n >= 2 and memo[n] == 0:
    		memo[n] = fibo(n-1) + fibo1(n-2)
    	return memo[n]
    	
    memo = [0] * (n+1)
    memo[0] = 0
    memo[1] = 1
    ```
    

## 6. DP

- 동적 계획 알고리즘은 최적화 문제를 해결하는 알고리즘
- 입력 크기가 작은 부분 문제들을 모두 해결 → 그 해들을 이용하여 큰 크기의 부분 문제들을 해결 → 원래 주어진 입력의 문제를 해결
    1. 문제를 부분 문제로 분할
    2. 가장 작은 부분 문제부터 해를 구함
    3. 결과를 테이블에 저장, 저장된 부분 문제의 해를 이용해 상위 문제 해를 구함
    
    ```
    def fibo2(n):
    	f = [0] * (n+1)
    	f[0] = 0
    	f[1] = 1
    	for i in range(2, n+1):
    	 f[i] = f[i-1] + f[i-2]
    	 
    	return f[n]
    ```
    
- 구현 방식
1. recursive 방식
2. iterative 방식

## 7. DFS(깊이우선탐색)

- 비선형구조인 그래프 구조는 그래프로 표현된 자료를 빠짐없이 검색하는 것이 중요

### DFS 방식

- 시작 지점에서 한 방향으로 깊이 탐색하다가 갈 곳이 없게 되면 마지막 갈림길로 돌아와 다른 방향으로 탐색을 반복
- 가장 마지막에 만났던 갈림길로 돌아가야 하므로 후입선출 구조인 스택을 사용

### 인접 행렬

- n x n 크기의 정사각형 행렬 = 노드들 간의 연결관계를 행렬로 표현한 것
- 무방향 그래프(이동하는 방향이 표시되지 않은 것)
    - 정점 i와 j 사이에 간선이 있다면 matrix[i][j] = matrix[j][i] = 1
    - 정점 i와 j 사이에 간선이 없으면 matrix[i][j] = matrix[j][i] = 0
    - 다 완성되면 대각선을 기준으로 대칭인 행렬이 됨

```
# 인접행렬 만들기
# V는 노드의 개수, E는 간선의 개수
V, E = map(int, input().split())

# 채울 간선 매트릭스
adj_matrix = [[0] * (V+1) for _ in range(V+1)]
# 노드별 간선 정보
data = list(map(int, input().split()))

# 간선의 개수만큼 반복하면서 간선 정보를 넣기
# input으로 들어오는 간선 정보는 2개씩 묶어서 들어옴(총 2*E개의 input)
# ex. (1 2) == 1은 2와 연결되어 있다
for i in range(E):
    n1 = data[i * 2]
    n2 = data[i * 2 + 1]
    adj_matrix[n1][n2] = 1
    adj_matrix[n2][n1] = 1      # 방향이 있는 경우 current -> next 방향에 맞게 값 넣기
```

### DFS 작동 구조

```
def DFS(s, V):        # s는 시작 정점, V는 정점 개수
    # stack에 시작 정점을 push
    stack = [s]
    # 방문 여부를 체크하는 리스트
    visited = [0] * (V + 1)

    # stack이 비면 중단 (stack에 값이 있는 동안 진행)
    while stack:
        # 현재 조사할 노드
        current = stack.pop()

        # 방문하지 않은 노드라면
        if visited[current] == 0:
            # 방문 표시
            visited[current] = 1
            # 방문한 노드 출력
            print(node[current])

            # 현재 노드에서 갈 수 있는 다음 노드들을 stack에 추가
            # 갈림길 중에서 먼저 탐색할 노드가 stack에 나중에 들어가기(stack은 후입선출)
            # range 범위로 정방향, 역순에 따라서 먼저 탐색할 노드를 결정할 수 있음(간선 방향)
            for next in range(V, 0, -1):
                # 다음 노드가 간선이 연결이 되어 있고, 방문한 적이 없다면
                if matrix[current][next] == 1 and visited[next] == 0:
                    # stack에 push
                    stack.append(next)
```

## 8. 계산기

- 중위표기법
    - 연산자를 피연산자의 가운데 표기하는 방법
- 후위표기법
    - 연산자를 피연산자 뒤에 표기하는 방법
    - ((A*B) - (C/D)) → AB*CD/-

### 중위표기법 → 후위표기법

1. 피연산자는 그대로 출력
2. 연산자는 스택이 비어있으면 추가
3. stack의 top이 자신보다 우선순위가 **낮은 연산자를 만날 때까지** pop 하고 자신을 담습니다.
    1. 단, 여는 괄호는 닫는 괄호가 아니면 pop하지 않습니다.
    2. 닫는 괄호가 나오면 여는 괄호가 나올 때까지 꺼내서 출력합니다.
4. 마지막에 도착하면 스택에서 차례로 꺼내서 출력합니다.

```
def calculator(expression):
    # 연산자의 우선 순위를 dict 형태로 구현
    op = {
        '+' : 1,
        '-' : 1,
        '*' : 2,
        '/' : 2,
    }
    result = ''
    stack = []

    for exp in expression:
        if exp.isdigit():   # 숫자면 바로 출력
            result += exp
        elif exp == '(':    # 여는 괄호면 스택에 넣기
            stack.append(exp)
        elif exp == ')':    # 닫는 괄호면 스택에서 여는 괄호 나올 때까지 빼서 출력
            while stack[-1] != '(':
                result += stack.pop()
            stack.pop()     # 여는 괄호도 스택에서 빼주기
        elif exp in op:     # 연산자라면 스택 안에 있는 연산자가 우선순위가 낮은 게 나올 때까지 빼서 출력
            while stack and op[exp] >= op[stack[-1]]:
                result += stack.pop()
            stack.append(exp)   # 다 뺐으면 자신을 스택에 넣기
    while stack:    # 연산 끝났으면 스택이 빌 때까지 결과 출력
        result += stack.pop()
    return result

def calcuation(expression):
    stack = []
    for exp in expression:
        if exp.isdigit():
            stack.append(int(exp))  # 숫자라면 int로 변환해서 스택에 넣기
        else:
            n2 = stack.pop()        # 연산자라면 스택에서 숫자 빼와서 연산
            n1 = stack.pop()
            if exp == '+':
                result = n1 + n2
            elif exp == '-':
                result = n1 - n2
            elif exp == '*':
                result = n1 * n2
            else:
                result = n1 / n2

            stack.append(result)    # 연산한 결과를 스택에 넣기
    return stack[-1]                # 마지막 결과 출력
```

### 후위표기법 스택으로 계산

1. 피연산자 만나면 스택에 push
2. 연산자 만나면 필요한 만큼 피연산자를 스택에서 pop → 연산결과를 다시 스택에 push

```
def calcuation(expression):
    stack = []
    for exp in expression:
        if exp.isdigit():
            stack.append(int(exp))  # 숫자라면 int로 변환해서 스택에 넣기
        else:
            n2 = stack.pop()        # 연산자라면 스택에서 숫자 빼와서 연산
            n1 = stack.pop()
            if exp == '+':
                result = n1 + n2
            elif exp == '-':
                result = n1 - n2
            elif exp == '*':
                result = n1 * n2
            else:
                result = n1 / n2

            stack.append(result)    # 연산한 결과를 스택에 넣기
    return stack[-1]                # 마지막 결과 출력
```

## 9. 백트래킹

- 해를 찾는 도중에 막히면 되돌아가서 다시 해를 찾는 방법
    - 최적화 문제(최댓값, 최솟값)와 결정 문제(조건을 만족하는지 yes or no)를 해결할 수 있음
- 노드의 유망성을 점검한 후 유망하지 않다면 백트래킹
    - 가지치기(prunning): 유망하지 않은 노드가 포함된 경로는 더 이상 고려하지 않음
- 백트래킹 vs DFS
    - 어떤 노드에서 출발하는 경로가 해결책이 될 것 같지 않으면 가지 않음으로 시도 횟수 줄임(Prunning 가지치기)
    - 경우의 수가 너무 많으면 DFS로는 불가능
    - 백트래킹을 사용하면 시간이 엄청 오래걸리는 경우도 발생가능

### 부분집합 만들기

```
def subset(k):  # 현재 배열의 인덱스
    if k == len(arr):  # 목표 도달: 배열의 모든 요소를 검사했을 때
        for i in range(len(arr)):
            if visited[i]:  # 방문한 요소만 출력
                print(arr[i], end=" ")
        print('끝')

    else:
        # 현재 요소를 부분 집합에 포함시키는 경우
        visited[k] = 1
        # print('채우는 중', visited)
        subset(k+1)

        # 현재 요소를 부분 집합에 포함시키지 않는 경우
        visited[k] = 0
        # print('빼는 중', visited)
        subset(k+1)

arr = [1,2,3]
visited = [0]* len(arr)  # 방문 여부를 저장하는 배열 초기화
```

### 부분집합의 합 (ex. 합 10)
```
def subset(k,sum_num):  # k는 현재 배열의 인덱스, sum_num은 부분집합의 합
    global cnt          # 함수를 호출한 횟수(디버깅용, 효율성 체크)
    cnt +=1
    
    if sum_num > 10:    # 가지치기 (합이 10 미만인 경우는 확인 X)
        return
    if k == n:          # 목표도달 (합이 10인 경우만 확인)
        if sum_num == 10:
            for i in range(n):
                if visited[i]:
                    print(arr[i], end=" ")
            print()
        return

    else:
        visited[k] = 1
        subset(k+1,sum_num+arr[k])    # 부분집합 요소를 추가하면 합에도 값을 추가
        visited[k] = 0
        subset(k+1,sum_num)

arr = [1,2,3,4]
n = len(arr)
cnt = 0
visited = [0]* n
subset(0,0)
```