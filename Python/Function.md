## 1. 함수 정의와 호출

```jsx
def greet(name):

	‘’’
	docstring

	‘’’
	message = 'Hello' + name
	return = messsage
	
result = greet('Alice')
print(result)
```

- parameter
    - 함수에 전달되는 값
- return을 만나면 함수가 종료
    - return이 없는 값은 파이썬이 자동으로 return None을 입력함
- 함수 호출
    - 필요한 경우 인자(argument) 전달
- print는 return이 없는 함수
    - print를 print하면 None이 나옴

## 2. parameter와 argument

- parameter
    - 함수를 정의할 때 함수가 받을 값을 나타내는 변수
- argument
    - 함수를 호출할 때 실제로 전달되는 값

## 3. argument 종류

### a. 위치 인자

- 함수 호출 시 인자의 위치에 따라 전달되는 인자
    - 반드시 값을 전달해야 함(아니면 오류 발생)

```jsx
def greet(name,age):
print(f'{name}님 {age}살')
```

### b. 기본 인자 값

- 함수 호출 시 인자를 전달하지 않으면 기본값이 매개변수에 할당됨

```jsx
def greet(name,age=30):
print(f'{name}님 {age}살')  #age 자리에 아무것도 안 넣으면 30살이 자동 출력
```

### c. 키워드 인자

- 호출 시 인자 이름과 함께 값을 전달
    - 인자 순서는 상관없고 인자 이름을 명시하여 전달
    - 호출 시 키워드 인자는 위치 인자 뒤에 있어야 함

```jsx
def greet(name,age=30):
print(f'{name}님 {age}살')

greet(name='Dave', age=35)
```

### d. 임의의 인자 목록

- 함수 정의 시 parameter 앞에 *를 붙여 여러 개 인자를 tuple로 처리
    - 만약 위치 인자를 사용하려면 *args보다 앞에 써야 함

```jsx
def calculate_sum(*args):
    print(args)
```

### e. 임의의 키워드 인자 목록

- 함수 정의 시 parameter 앞에 **를 붙여 여러 개 인자를 dictionary로 처리

```jsx
def print_info(**kwargs):
    print(kwargs)

print_info(name='Dave', age=30) # 작성할 때도 키워드 인자 작성할 때처럼
```

### 함수 인자 권장 순서

위치 →  기본 → 가변 → 가변 키워드

```jsx
def func(pos1, pos2, default_arg='default', *args, **kwargs):
```

## 4. 재귀 함수

자기 자신을 호출하는 함수 (ex. 팩토리얼)

- base case : 종료되는 상황
- 특정 알고리즘 식 표현할 때 변수 사용이 줄고 코드 가독성이 높아짐
- 1개 이상의 base case가 존재하고 반복되는 조건이 base case를 향할 수 있도록

## 5. 내장 함수(built-in 함수)

파이썬이 기본적으로 제공하는 함수(import 없이 바로 사용 가능)

- 자주 사용되는 내장 함수
    - print, len, max, min, sum, sorted
    - 외우지 말고 필요할 때 찾아서 쓰기

### map 함수

- map(function, iterable(반복 가능한 객체 ex. 컬렉션))
    - map(function, iterable, iterable, iterable, ….)  # 여러 개의 iterable을 적용할 수도 있음

함수를 반복하는 형태

```jsx
numbers1 = input().split()
print(numbers1) # ['1', '2', '3']

numbers2 = list(map(int, input().split()))
print(numbers2) # [1, 2, 3]
```

### zip 함수

zip(*iterables)

임의의 iterable을 모아 튜플을 원소로 하는 zip object를 반환

여러 개 리스트를 동시에 조회할 때

```jsx
scores = [
    [10, 20, 30],
    [40, 50, 39],
    [20, 40, 50],
]
for score in zip(*scores):
    print(score)
    
(10, 40, 20)
(20, 50, 40)
(30, 39, 50)
```

```jsx
#list comprehension
many_user = [create_user(name, age, address) for name, age, address in zip(name, age, addresss)]

```

## 6. 함수와 Scope

- scope
    - global scope(전역)
        - 코드 어디에서든 참조할 수 있는 공간
    - local scope(지역)
        - 함수가 만든 scope(함수 내부에서만 참조 가능)
- variable
    - global variable
    - local variable
- 변수 수명주기
    - built-in scope
        - 파이썬이 실행된 이후부터 영원히
    - global scope
        - 모듈이 호출된 시점, 인터프리터가 끝날 때까지(built-in과 유사)
    - local scope
        - 함수가 호출될 때 생성 → 종료될 때까지

**<LEGB 룰>**
- Local → Enclosed → Global → Built-in
- 안쪽에서 바깥쪽으로 찾음
- built-in scope 변수는 변수명으로 사용 금지

### global

변수의 scope를 전역 범위로 지정, 함수 내에서 함수 바깥의 변수도 수정 가능

- 주의사항
    - 키워드 선언 전에 참조 불가
    - 매개변수에는 사용 불가
    
    ```jsx
    def increment():
        global num  # num를 전역 변수로 선언
        num += 1
    ```
    

## 7. packing & unpacking

여러 개 값을 하나의 변수로 묶기 (ex. 변수에 담긴 값은 tuple로 묶임)

- *활용한 packing (일부를 묶을 때는 리스트로 묶임)
    
    ```jsx
    numbers = [1, 2, 3, 4, 5]
    a, *b, c = numbers
    print(a)  # 1
    print(b)  # [2, 3, 4] <-- 리스트로 묶음
    print(c)  # 5
    ```
    

- 튜플이나 리스트 등의 객체 요소들을 개별 변수에 할당 - unpacking
    - 변수 갯수와 요소 갯수는 맞춰야 함
    
    ```jsx
    packed_values = 1, 2, 3, 4, 5
    a, b, c, d, e = packed_values
    print(a, b, c, d, e)  # 1 2 3 4 5
    ```
    
- *args  unpacking
    - 갯수 맞춰야 함
    
    ```jsx
    def my_function(x, y, z):
        print(x, y, z)
    
    names = ['alice', 'jane', 'peter']
    my_function(*names)  # alice jane peter
    ```
    
- ** 활용 unpacking
    - dictionary 키-값 쌍을 unpacking 하여 함수의 키워드 인자로 전달
    - dictionary 키와 parameter 이름이 같아야 함
    
    ```jsx
    def my_function(x, y, z):
        print(x, y, z)
    
    my_dict = {'x': 1, 'y': 2, 'z': 3}
    my_function(**my_dict)  # 1 2 3
    ```
    

## 8. lambda 표현식

익명 함수, 한 줄로 짧게 함수 작성할 때

- lambda 매개변수: 표현식
    - ()사용하지 않음
    - 매개변수가 많을 경우 ,로 구분
- map이랑 유용하게 사용
    
    ```jsx
    def square(x):
        return x**2
        
    squared1 = list(map(square, numbers))
    
    squared2 = list(map(lambda x: x**2),numbers)
    ```
    
- 한 곳에서만 사용, 한 줄로 작성하고 싶을 때 사용