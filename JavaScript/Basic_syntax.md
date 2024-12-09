## 1. 원시 자료형

- 변수에 값이 직접 저장되는 자료형 - 불변, 값이 복사
1. Number
    
    ```jsx
    const e = Infinity      // 최대 수
    const f = -Infinity
    const g = NaN           // Not a Number을 나타내는 값
    ```
    
    - NaN 반환 예시
        1. 숫자로서 읽을 수 없음 (undefined)
        2. 결과가 허수인 경우
        3. 피연산자가 NaN(7 ** NaN)
        4. 정의할 수 없는 계산식 (0 * Infinity)
        5. 문자열을 포함하면서 덧셈이 아닌 계산식 (’가’ / 3)
2. String
    - + 연산자로 문자열끼리 결합 (뺄셈, 곱셈, 나눗셈 X)
3. Template literal
    - Backtick(``)이용, 여러 줄에 걸쳐 문자열 정의 가능, 변수를 문자열 안에 바로 연결 가능
    
    ```jsx
    const age = 100
    const message = `홍길동은 ${age}세입니다.`
    ```
    
4. null vs undefined
    - null - 개발자가 의도적으로 값이 없음을 나타낼 때
        - object 타입임 (역사적 이유 때문) but 원시 자료형이긴 함
    - undefined - 시스템이나 JS 엔진이 값이 할당되지 않음을 나타낼 때
5. Boolean
    - 조건문 또는 반복문에서 Boolean이 아닌 데이터 타입은 자동 형변환 규칙으로 Boolean 변환
    
    | 데이터 타입 | false | true |
    | --- | --- | --- |
    | undefined | 항상 false | X |
    | null | 항상 false | X |
    | Number | 0, -0, NaN | 나머지 모든 경우 |
    | String | (빈 문자열) | 나머지 모든 경우 |

### 증가/감소 연산자(++, --)

- 피연산자를 증가/감소시키고 연산자 위치에 따라 증가하기 전이나 후의 값을 반환
    - 앞에 쓰면) 반환 → 연산 / 뒤에 쓰면) 연산 → 반환
- +=, -=처럼 명시적으로 표현하는 것을 권장, but 자주 사용함

### 동등 연산자(==)(권장 X)

- 두 피연산자가 같은 값으로 평가되는지 비교 후 boolean 값 반환
- 암묵적 타입 변환 → 타입 일치시킨 후 비교

### 일치 연산자(===)

- 두 피연산자가 값과 타입이 모두 같은 경우 true 반환

### 논리 연산자(단축 평가 지원)

- &&(and)
- ||(or)
- !(not)

### if 조건문

```jsx
if (name === 'admin') {
  console.log('관리자님 안녕')
} else if (name === 'customer') {
  console.log('고객님 안녕')
} else {
  console.log(`반갑습니다. ${name}`)
}
```

### 3항 연산자

```jsx
condition ? expression1 : expression2
```

- condition - 평가 조건(true / false)
- expression1 - true일 경우 반환할 값 or 표현식
- expression2 - false일 경우 반환할 값 or 표현식

### 반복문

1. while

```jsx
let i = 0

while (i < 6) {
  console.log(i)
  i += 1
}
```

1. for
- 초기문 실행 → 조건문 평가 후 코드 블럭 실행 → 증감문 실행 → 조건문 평가 후 코드 실행 …
- 증감문에서 최초 정의한 변수를 재할당하면 let 사용

```jsx
for ([초기문]; [조건문]; [증감문]) {
console.log(i)
}

for (let i = 0; i < 6; i++) {
  console.log(i)
}
```

1. for … in
- 객체의 열거 가능한 속성(property)에 대해 반복
    - 순서가 없는 객체에 사용 (순서가 중요한 배열에서는 사용 X)
    - const 사용 가능(재할당X, 매 반복마다 다른 속성 이름이 변수에 지정)

```jsx
const object = {
  a: 'apple',
  b: 'banana'
}

// Object의 Key를 순회하는 반복문
for (const property in object) {
  console.log(property)          // a b
  console.log(object[property])  // apple banana
}
```

1. for … of
- 반복 가능한 객체(배열, 문자열 등)에 대해 반복

```jsx
const numbers = [0, 1, 2, 3]

for (const number of numbers) {
  console.log(number)     // 0 1 2 3
}

const myStr = 'apple'

for (const char of myStr) {
  console.log(char)     // a p p l e
}
```

```jsx
// for in 와 for of 의 차이
const arr = ['a', 'b', 'c']

for (const i in arr) {
  console.log(i) // 0, 1, 2
}

for (const i of arr) {
  console.log(i) // a, b, c
}
```

## 2. 참조 자료형

- 객체의 주소가 저장되는 자료형 - 가변, 주소가 복사

## 1) 함수

- return 값이 없다면 undefined를 반환

### 선언식

- 호이스팅 됨
- 코드 구조와 가독성 면에서 표현식보다 명확함

```jsx
function add (num1, num2) {
  return num1, num2
}
```

### 표현식(권장)

- 호이스팅 안 됨 → 코드 실행 흐름을 더 명확하게 예측
    - 변수 선언만 호이스팅, 함수 할당은 실행 시점에 이뤄짐
- 함수 이름이 없는 익명 함수를 사용할 수 있음
- 변수이기 때문에 함수를 값으로 활용 쉬움
- let, const로 스코프 관리가 용이

```jsx
const sub = function (num1, num2) {
  return num1 - num2
}
```

### 매개변수

- 기본 함수 매개변수

```jsx
// 매개변수를 전달하지 않았을 때 기본값 설정
const greeting = function (name = 'Anonymous') {
  return `Hi, ${name}`
}
greeting()
```

- 나머지 매개변수
    - 임의의 수의 인자를 배열로 허용하여 가변 인자를 나타냄
    - 함수 정의 시 하나만 작성할 수 있음
    - 함수 정의에서 매개변수 마지막에 위치

```jsx
const myFunc = function (params1, params2, ...restParams) {
  return [params1, params2, restParams]
}
console.log(myFunc(1, 2, 3, 4, 5))    // 3, 4, 5를 배열로 묶어서 출력
console.log(myFunc(1, 2))             // [] 빈 배열 하나도 출력
```

- 매개변수 개수 > 인자 개수
    - 누락된 인자는 undefined로 할당
- 매개변수 개수  < 인자 개수
    - 초과 입력한 인자는 사용 X

### Spread syntax(…)

- 배열이나 문자열처럼 반복 가능한 항목을 펼치는 것
    - 배열이나 객체 요소를 개별적인 값으로 분리
    - 다른 배열이나 객체 요소를 현재 배열/객체에 추가 등
- 활용
    1. 함수 호출 시 인자 확장 (함수 호출 시)
    2. 나머지 매개변수 (함수 선언 시)

### 화살표 함수

- 자신만의 this를 가지지 않음 → 자신의 부모 함수 or 부모 객체의 this값을 가져옴

```jsx
const arrow1 = function (name) {
      return `hello, ${name}`
    }
// 1. function 키워드 삭제 후 화살표 작성
const arrow2 = (name) => {
  return `hello, ${name}`
}

// 2. 인자의 소괄호 삭제 (인자가 1개일 경우에만 가능)
const arrow3 = name => {
  return `hello, ${name}`
}

// 3. 중괄호와 return 삭제 (함수 본문이 return을 포함한 표현식이 1줄일 경우에만 가능)
const arrow4 = name => `hello, ${name}`

-------------------------------------------------------------------------------------
// 특수 케이스
// 1. 인자가 없다면 () or _로 표시 가능
const noArgs1 = () => 'No args'
const noArgs2 = _ => 'No args'

// 2-1. object를 return 한다면 return 명시적으로 작성
const returnObject1 = () => {
	return { key : 'value' }
}

// 2-2. return을 작성하지 않으려면 객체를 소괄호로 감싸야 함
const returnObject2 = () => ({ key: 'value' })
```

## 2) 객체(object)

- 키로 구분된 데이터 집합을 저장하는 자료형
- key는 문자형만, value는 모든 자료형 가능

```jsx
// 조회 (key에 띄어쓰기가 있다면 []로, 나머지는 .으로)
user['key with space']
'greeting' in user    // 존재 여부 확인

// 삭제
delete user.name
```

### Method

- object.method() 방식으로 호출 → 객체를 행동할 수 있게 함

```jsx
const person = {
  name: 'Alice',
  greeting: function () {
    return `Hello my name is ${this.name}`
  },
}

console.log(person.greeting())
```

### this

- 함수나 메서드를 호출한 객체를 가리키는 키워드
- 함수 내에서 객체의 속성, 메서드에 접근하기 위해 사용
- 동적 할당 - 호출하는 방식에 따라 값이 결정됨
    - 단순 호출 → 전역 객체에 접근(Window)
    - 메서드 호출 → 메서드를 호출한 객체에 접근
        - 장점: 함수(메서드)를 여러 객체에서 재사용 가능
        - 단점: 실수로 이어질 수 있음

```jsx
// 2. 중첩된 함수에서의 this
// 2.1 일반 함수
const myObj2 = {
  numbers: [1, 2, 3],
  myFunc: function () {
    this.numbers.forEach(function (number) {     // 이 this는 mbObj2를 가리킴
      console.log(this)                          // 이 this는 window를 가리킴
    })
  }
}

// 2.2 화살표 함수 (자기보다 한 단계 상위의 this를 가리킴)
const myObj3 = {
  numbers: [1, 2, 3],
  myFunc: function () {
    this.numbers.forEach((number) => {
      console.log(this)                         // 이 this는 myObj2를 가리킴
    })
  }
}
```

### 객체 관련 추가 문법

1. 단축 속성

```jsx
const name = 'Alice'
const age = 30

const user1 = {
  name: name,
  age: age
}
```

1. 단축 메서드

```jsx
const myObj1 = {
  myFunc: function () {
    return 'Hello'
  }
}

// 단축
const myObj1 = {
  myFunc() {
    return 'Hello'
  }
}
```

1. Computed Property
- 변수 or 함수를 key 값으로 사용

```jsx
const product = prompt('물건 이름을 입력해주세요')
const prefix = 'my'
const suffix = 'property'

const bag = {
  [product]: 5,
  [`${prefix} ${suffix} ${product}`]: true
}

// 원리
let you = {
    name: "super",
    id: 123,
}

let temp = 'id'

you.id        // 123
you['id']     // 123
you[temp]     // 123
```

1. 구조 분해 할당
- 변수들을 **중괄호로** 감싸서 할당
- 할당할 변수의 이름이 같아야 함

```jsx
const userInfo = {
  firstName: 'Alice',
  userId: 'alice123',
  email: 'alice123@gmail.com'
}

const { firstName, userId, email } = userInfo
```

```jsx
// 함수의 인자에도 변수들의 이름이 같다면 한 번에 넣을 수 있음 (인자를 { }로 묶어주기)
function printInfo({ name, age, city }) {
  console.log(`이름: ${name}, 나이: ${age}, 도시: ${city}`)
}

const person = {
  name: 'Bob',
  age: 35,
  city: 'London',
}

console.log(printInfo(person))
```

1. 전개 구문
- 객체 복사 - 얕은 복사에 활용

```jsx
const obj = { b: 2, c: 3, d: 4 }
const newObj = {a:1, ...obj, e:5}
```

1. key & value 추출

```jsx
const profile = {
  name: 'Alice',
  age: 30
}

console.log(Object.keys(profile)) // ['name', 'age']
console.log(Object.values(profile)) // ['Alice', 30]
```

1. Optional chaining(?.)
- 메서드나 속성이 뒤 속성을 가지고 있는지 먼저 평가
    - 참조 대상이 null 또는 undefined라면 에러 대신 평가를 멈추고 undefined 반환
- 장점: 참조가 누락될 가능성이 있는 경우 연결된 속성으로 접근할 때 더 짧고 간단하게 표현
- 주의사항
    1. 존재하지 않아도 괜찮은 대상에만 사용 - 왼쪽 평가대상이 없어도 괜찮은 경우만
    2. Optional chaining 앞의 변수는 반드시 선언되어야 함

```jsx
const user = {
  name: 'Alice',
  greeting: function () {
    return 'hello'
  }
}

console.log(user.address.street) // Uncaught TypeError: Cannot read properties of undefined (reading 'street')
console.log(user.address?.street) // Undefined

console.log(user.address && user.address.street)   // 위랑 같은 구문

-------------------------------------------------------------------------------------
// 있으면 반환, 없으면 undefinded 반환
user.obj?.property
user.obj?.[property]
user.obj?.method()
```

### JSON

- key-value 형태로 이루어진 문자열 → Object 자료형으로 변경해야 함

```jsx
const jsObject = {
  coffee: 'Americano',
  iceCream: 'Cookie and cream'
}

// Object -> JSON
const objToJson = JSON.stringify(jsObject)
console.log(objToJson)  // {"coffee":"Americano","iceCream":"Cookie and cream"}
console.log(typeof objToJson)  // string

// JSON -> Object
const jsonToObj = JSON.parse(objToJson)
console.log(jsonToObj)  // { coffee: 'Americano', iceCream: 'Cookie and cream' }
console.log(typeof jsonToObj)  // object
```

### Class

```jsx
class Member {
  constructor(name, age) {     // 생성자 함수
    this.name = name
    this.age = age
  }
  sayHi() {
    console.log(`Hi, I am ${this.name}`)
  }
}

const member2 = new Member('김', 13)     // 생성자 함수나 클래스로 새로운 객체 생성
member2.sayHi()

const instance = new Member(arg1, arg2)   // new 연산자로 클래스의 constructor()를 호출
```

---

## 3) 배열(Array)

- 순서가 있는 데이터 집합 ([ ]로 작성), index를 key로 가짐
- 요소의 자료형은 제약 없음
- length 속성 있음

### 콜백 함수

- 다른 함수에 인자로 전달되는 함수
- 장점
    - 함수의 재사용성
    - 비동기 처리
        - 콜백함수가 들어가 있는 함수는 call stack으로 보내고 다음 코드를 먼저 처리 후 나중에 결과 받아옴

### Method 정리

1. 추가/삭제

| 메서드 | 기능 |
| --- | --- |
| push, pop | 맨 뒤에 추가/삭제 |
| unshift, shift | 맨 앞에 추가/삭제 |

2. Array Helper Method

| 메서드 | 기능 |
| --- | --- |
| forEach | - 배열 내 모든 요소 각각에 콜백함수 호출
- 반환 값 없음
- break, continue 사용 불가(some, every 사용) |
| map | - 배열 내 모든 요소 각각에 콜백함수 호출
- 함수 호출 결과를 모아서 새로운 배열 반환 |

```jsx
const names = ['Alice', 'Bella', 'Cathy']
const roles = ['captain', 'sub1', 'sub2']

names.forEach((name, index, arr) => {       // 값, 인덱스, 배열 전체를 사용 가능
	names.console({'key': name, 'val': roles[index]})
})

const result4 = names.map((name, index, arr) => {    // 값, 인덱스, 배열 전체를 사용 가능
  return {name, index}     // 단축 구문
})
```

3. some() / every()

```jsx
const array = [1, 2, 3, 4, 5]

// - 배열의 요소 중 적어도 하나라도 콜백 함수를 통과하는지 테스트
// - 콜백 함수가 배열 요소 적어도 하나라도 참이면 true를 반환, 거짓이면 false 반환 -> 중지
const isEvenNumber = array.some(function (number) {
  console.log(number)
  return number % 2 === 0
})

// - 배열의 모든 요소가 콜백 함수를 통과하는지 테스트
// - 콜백 함수가 모든 배열 요소에 대해 참이면 true를 반환, 거짓이면 false 반환 -> 중지
const isAllEvenNumber = array.every(function (number) {
  console.log(number)
  return number % 2 === 0
})
```

4. find() / filter()

```jsx
// 객체 안에 요소들을 하나씩 검사
arr.filter((params) => params.includes('easy'))  // 조건 만족하는 모든 요소 찾아서 객체 반환
arr.find((params) => params.includes('easy'))    // 조건 만족하는 첫 번째 요소 반환
```