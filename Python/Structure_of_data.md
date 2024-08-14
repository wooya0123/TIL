## 자료 구조

데이터의 효율적인 저장 관리를 위해 구조를 나눠 놓은 것

## Sequence 데이터 구조

### 1. Method

객체에 속한 함수

- 특징
    - class에 속해 있는 함수(다 class라고 부름)
    - class는 타입을 표현하는 방법
    - 데이터 타입 별로 다양한 기능 존재
    - method는 이어서 사용 가능
        - 앞 쪽 반환값이 있고 데이터 타입이 맞아야 함
    
    ```
    new_text = text.swapcase().replace('l', 'z')
    ```
    

### 문자열 Method

변경 후 반환하기 때문에 새 문자열이 생성

**<문자열 조회 탐색>**

- s.find(x)
    - x의 첫 번째 위치를 반환, 없으면 -1 반환
- s.find(x)
    - x의 첫 번째 위치를 반환, 없으면 오류 발생
- s.isupper() / s.islower()
    - 문자열이 모두 다  대 / 소문자인지
    - T / F 반환
    - 함수에 is가 붙으면 반환값이 T/F인 경우가 대부분
- s.isalpha()
    - 알파벳 문자 여부
    - 유니코드 상 letter(한국어도 포함)
    - T / F 반환

**<문자열 조작(새 문자열 반환)>**

- **s.replace(old, new[, count])**
    - 바꿀 대상 글자를 새로운 글자로 바꿔서 반환
    - count 선택인자를 넣지 않으면 전체 다 바꿈
- **s.strip([chars])**
    - 공백이나 특정 문자 제거
    - 아무것도 입력하지 않으면 공백 제거
    - 맨 앞과 맨 뒤의 문자열 or 문자열 덩어리만 제거 가능
- **s.split(sep = None, maxsplit = -1)**
    - 지정한 문자를 구분자로 문자열을 분리 → 문자열의 리스트로 반환
    - sep(구분자)에 아무것도 안 넣으면 공백을 기준으로 분리
    - 구분자를 넣을 때 공백이 살아있을 수 있으므로 주의
- **‘seperator’.join(iterable)**
    - 구분자로 iterable를 문자열로 결합
    
    ```
    words = ['Hello', 'world!']
    new_words = '-'.join(words)
    ```
    

---

- s.capitalize()
    - 가장 첫 번째 글자를 대문자로 변경 후 뒤는 모두 소문자로
- s..title()
    - 가장 첫 번째 글자, 공백 뒤 글자를 대문자로 변경
- s. upper() / s.lower()
    - 모두 대 / 소문자로 변경
- s.swapcase()
    - 기존 대 / 소문자를 소 / 대문자로 변경

**<문자열에 포함된 유형 판별>**

- s.isdecimal()
    - 일반적인 십진수 숫자만 True
- s.isdigit()
    - 지수 표현도 True
- s.isnumeric()
    - 일반 숫자, 로마 숫자, 분수, 지수 등 다양한 형태의 숫자 표현도 True


### 리스트 Method

**<리스트 값 추가, 삭제>**

- L.append(x)
    - 리스트 마지막에 항목 x 추가(x형태 그대로 추가)
    - 인자를 1개만 받을 수 있음
    - 원본 변경, 반환 X
- L.extend(x)
    - iterable x의 모든 항목들을 풀어서 리스트 끝에 추가(+=과 같은 기능)
    - 인자를 1개만 받을 수 있음
    - 원본 변경, 반환 X
- L.insert(i, x)
    - 인덱스 i자리에 x를 삽입
    - 원본 변경, 반환 X
- L.remove(x)
    - 리스트의 첫 번째 일치하는 항목을 삭제
    - 원본 변경, 반환 X
- L.pop(i)
    - 인덱스 i번째 항목 제거하고 반환
    - i 작성하지 않을 경우 마지막 항목 제거
- L.clear()
    - 리스트 모든 항목 삭제
    - 원본 변경, 반환 X

**<리스트 탐색, 정렬>**

- L.index(x)
    - 리스트에서 첫 번째 일치하는 항목 x의 인덱스를 반환
- L.count(x)
    - 리스트에서 항목 x의 개수를 반환
- L.reverse()
    - 리스트 순서를 역순으로 변경(정렬 X)
    - 원본 변경, 반환 X
- L.sort()
    - 리스트를 오름차순으로 정렬
    - 내림차순: L.sort(reverse = True)

### 2. Copy

가변 데이터 타입과 불변 데이터 타입을 다르게 다룸

- 할당
    - 가변 데이터인 경우
        - 같은 주소를 바라보기 때문에 하나의 값이 바뀌면 모두 바뀜
    - 불변 데이터인 경우
        - 바라보는 주소의 값이 불변이기 때문에 하나가 바뀌어도 다른 하나는 이전 주소를 그대로 바라봄
- 얕은 복사
    - 복사한 값을 바꿔도 원본은 바뀌지 않음
        - slicing으로 리스트 전체를 잘라서 할당
        - L.copy() 사용
    - 한계
        - 복사한 값의 리스트 속에 리스트의 값은 바꿀 수 없음
        - 서브 리스트는 같은 리스트 주소를 바라보기 때문
- 깊은 복사
    - copy 모듈 활용하여 copy.deepcopy(x)
    - 복사한 값의 리스트 속에 리스트 값도 변경 가능
    - 서브 리스트도 다른 주소를 바라보는 아예 다른 리스트이기 때문

---

## Non-Sequence 데이터 구조

### 딕셔너리 Method

- d.clear()
    - 모든 키/값 쌍을 제거
- **d.get(key[,default])**
    - key가 연결된 value를 반환, key가 없으면 None 혹은 default값 반환
    - dict[’key’]는 없는 값에서 오류 나므로 오류 안 나게 할 때 사용
- **d.keys()**
    - key값만 뽑아내기
    - 리스트로 형 변환 하지 않고 반복을 돌릴 수 있음
- **d.values()**
    - value 값만 뽑아내기
    - 리스트로 형 변환 하지 않고 반복을 돌릴 수 있음
- **d.items()**
    - (key, value) 튜플 형태로 묶여서 나옴
    - unpacking해서 반복문 돌리고 key, value값을 사용할 수 있음
    
    ```
    for key, value in person.items():
        print(key)  # name, age
        print(value)  # Alice, 25
    ```
    
- **d.pop(key[,default])**
    - key를 제거하고 연결됐던 value 값을 반환, 없으면 에러나 default 반환
    - key가 없는데 pop하면 key에러 발생
- d.setdefault(key[,default])
    - key와 연결된 value값을 반환
    - key가 없으면 default를 value로 하는 key를 딕셔너리에 추가, default값 반환
    
    ```
    print(person.setdefault('country', 'KOREA'))    # KOREA
    print(person)   # {'name': 'Alice', 'age': 25, 'country': 'KOREA'}
    ```
    
- d.update([other])
    - 딕셔너리를 통째로 넣거나 key-value 형태로 넣을 수 있음
    - 겹치는 것이 있으면 새로운 값으로 변경, 없으면 추가
    
    ```
    person.update(age = 100, country = 'KOREA')
    person.update({age : 100})
    ```
    

### 세트 Method

- **s.add(x)**
    - 세트는 순서가 없기 때문에 랜덤한 자리에 추가
- s.clear()
    - 값을 모두 제거    # set()
- **s.remove(x)**
    - 해당 값을 제거
    - 없는 값을 제거하면 key에러가 발생
- s.pop()
    - 임의의 요소 제거 (무작위가 아니라 해시 테이블에 나타나는 순서대로 반환)
- s.discard(x)
    - 해당 값을 제거
    - 없는 값을 제거하면 에러 없이 그냥 원래 값 그대로 출력
- s.update(iterable)
    - iterable의 요소들을 풀어서 넣어줌

### 세트 집합 Method

- set1.difference(set2)
    - set1 - set2 차집합
    - set1에는 들어있지만 set2에는 없는 항목으로 set생성 후 반환
- set1.intersection(set2)
    - set1 & set2 교집합
    - set1과 set2에 모두 들어있는 항목으로 set 생성 후 반환
- set1.union(set2)
    - set1 | set2 합집합
    - set1 또는 set2에 들어있는 항목으로 set 생성 후 반환
- set1.issubset(set2)
    - set1 <=set2
    - set1의 항목이 모두 set2에 들어있으면 True 반환
- set1.issuperset(set2)
    - set1 >= set2
    - set1이 set2의 항목을 모두 포함하면 True 반환

### Hash Table

해시 함수를 사용해 변환한 값을 index로 삼아 key-value를 저장하는 자료 구조

- key를 해시 값으로 변환하고 이를 index로 사용해 데이터를 저장, 검색
    - 해시 함수는 파이썬을 실행 시마다 재구성됨 (=index가 바뀔 수 있음)
    - 해시 테이블은 순서가 있음
        - 정수는 항상 같은 해시 값 = 해시 테이블의 위치가 항상 같음
            - 입력된 정수값을 해시 테이블의 위치 값으로 그대로 사용
        - 문자열은 매번 해시 값이 달라짐 = 해시 테이블의 위치가 매번 달라짐


- Hash
    - 임의의 크기를 가진 데이터(key)를 고정된 크기의 고유한 값으로 변환
    - 데이터에 대한 지문 같은 역할, 고유하게 식별
    - hash값은 정수
- 세트와 딕셔너리
    - set 요소는 해시 함수를 통해 해시 테이블에 저장
    - dictionary key값은 해시 함수를 통해 value는 해시 테이블에 저장
    - 해시 테이블 상에 순서가 있다고 해도 순서가 없는 자료형임

### Hashable

- hash() 함수의 인자로 전달해서 결과를 반환 받을 수 있는 객체
    - 대부분 불변형 데이터 타입
    - tuple의 경우 요소로 가변형을 가질 경우 hash 함수 불가
- 해시 테이블의 키는 불변해야 함
    - 값이 변경될 수 있기 때문에 해시 값이 변경될 가능성이 있음
    - 같은 객체에 다른 해시 값이 반환될 수 있음
- hashable 객체가 필요한 이유
    1. 해시 테이블 기반 자료 구조 사용
        1. set와 dict의 key
        2. 중복 값 방지
        3. 빠른 검색과 조회
    2. 불변성을 통한 일관된 해시 값
    3. 안정성, 예측 가능성 유지

### 파이썬 문법 규격

EBNF 표기법

- 확장된 프로그래밍 언어의 문법 표기법(메타 기호를 추가해서 간결하고 표현력이 강해짐)
- 문서 상에 표기할 때 통일하여 작성하기 위함