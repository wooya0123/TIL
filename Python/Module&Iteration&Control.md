## 1. 모듈

한 파일로 묶인 변수와 함수의 모음

특정 기능을 하는 코드가 작성된 파이썬 파일(.py)

- 내장 모듈도 존재(ex. math)
- from과 import를 사용하여 접근
    - 2개 이상의 패키지를 접근할 때
        - from 패키지. 서브패키지 import 모듈
        print(모듈. 함수())
- 모듈 사용하기
    - . 왼쪽 객체에서 .의 오른쪽 이름을 찾아라
- 서로 다른 모듈이 같은 이름의 함수를 제공할 경우 문제
    - 마지막 import된 이름으로 대체 → 별칭을 부여해서 이름을 바꿔줘야 함
    - as 키워드로 별칭 부여

### 사용자 정의 모듈

- 만들어둔 .py 파일에 있는 함수를 불러와서 사용
    
    ```
    import my_math
    print(my_math.add(1, 2))
    
    from my_math import add
    print(add(1,2))
    ```
    

### 라이브러리

- 패키지
    - 연관된 모듈을 하나의 디렉토리에 모아 놓은 것
    - 모듈들의 이름 공간을 구분하여 충돌 방지
    - 모듈들을 효율적으로 관리, 재사용 목적
- 라이브러리
    - 패키지와 모듈을 모은 것
- 파이썬 내장 라이브러리(PSL 내부 패키지)
    - 파이썬에서 기본으로 제공하는 라이브러리
    - 설치 없이 바로 import
- 외부 패키지
    - pip 사용하여 설치 후 import
    - 인터넷이 연결되어야 설치 가능
    - 설치 방법
        - 버전 명시 X → 최신 버전
        - 버전 명시 ( == )→ 특정 버전
        - 특정 버전 이상 ( > =) → 그 이상의 버전
        - **pip install ____**
    - global에 설치됨
    - requests 패키지(중요한 패키지)
        - 외부 api로 요청을 보내는 패키지
        - api에 있는 정보를 dictionary 형태로 가져올 수 있게 해줌

## 2. 제어문

코드 실행 흐름을 제어

조건에 따라 실행 or 반복적으로 코드 실행

### 조건문

**if 문**

- 중첩 조건문도 가능

```jsx
if dust >150:
    print('매우 나쁨')
    if dust > 300:
        print('위험해요')
elif dust > 80:
    print('나쁨')
else:
    print('보통')
```

### 반복문

**for 문**

- 반복 횟수가 명확하게 정해져 있는 경우
    - ex. list, tuple, 문자열 등 sequence 형식 데이터 처리
- for 변수 in 반복 가능한 객체(sequence객체, dict, set 등)
코드~~

```
for item in items:     # 복수형 안에서 단수형 맞춰서 작성해주길
    print(item)
```

- 문자열도 sequence이므로 반복 가능
- range는 그 자체가 sequence라서 list로 바꾸지 않아도 됨
- dictionary는 key가 기본적으로 출력
    - index로 value 값 얻을 수 있음

```python
for key in my_dict:
    print(key)
    print(my_dict[key])
```

- index를 통해서 접근할 때

```python
numbers = [4, 6, 10, -8, 5]

for i in range(len(numbers)):
    numbers[i] = numbers[i] * 2
```

- 중첩 리스트 순회
    - print가 호출되는 횟수 len(바깥 거) * len(안에 거)
    - 일단 1개를 작성하고 완성해보기

```python
elements = [['A', 'B'], ['c', 'd']]

for elem in elements:
    for item in elem:
        print(item)
```

**while 문**

- 반복 횟수가 불명확, 조건에 따라 반복 종료해야 할 때
    - 조건이 거짓이 될 때까지 반복

### 제어문

**break**

- 반복을 즉시 중지
- 프로그램 종료 조건 만들기
    
    ```
    numbers = [1, 3, 5, 6, 7, 9, 10, 11]
    found_even = False
    
    for i in numbers:
        if i % 2 == 0:
            print(f'첫 번째 짝수{i}를 찾았습니다.')
            found_even = True
            break
            
    if found_even == False:
        print('짝수를 찾지 못함')
    ```
    

**continue**

- 해당 반복을 skip
    
    ```
    numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    
    for i in numbers:
        if i % 2 == 0:
            continue
        print(i)
    ```
    

**pass**

- 코드 작성 중 미완성 부분에 넣어줄 때
- 아무 동작 없이 넘어갈 때
- 무한 루프에서 조건이 충족되지 않을 때

## 3. List Comprehension

- 목적
    - 리스트 생성
    - 남용하지 말 것
    - for 문과 list comprehension을 왔다갔다 할 수 있어야 함
- 예시
```
[expression for 변수 in iterable if 조건식]
list(expression for 변수 in iterable if 조건식)
```

- 반복하는데 변수를 굳이 안 써도 될 경우
    - 변수 자리에 '_' 작성
- 리스트 생성법 3가지
    1. for 문
    
    ```python
    result1 = []
    for i in range(10):
        result1.append(i)
    ```
    
    1. list comprehension
    
    ```
    result2 = [i for i in range(10)]
    # result2 = list(i for i in range(10))
    ```
    
    1. map
    
    ```jsx
    result3 = list(map(lambda i: i, range(10)))
    ```
    

## 4. enumerate (내장함수)

반복을 돌면서 index 값도 필요할 때

- start = 0 값을 변경하면 출력에 나오는 시작 index 번호를 바꿀 수 있음
```
fruits = ['apple', 'banna', 'cherry']

for index, fruit in  enumerate(fruits):
	print(f'인덱스 {index}: {fruit}')

"""
0: apple
1: banana
2: cherry
"""
```