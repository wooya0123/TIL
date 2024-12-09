## 1. 변수 선언

### 블록 스코프

- 중괄호 내부
- 블록 스코프를 가지는 변수는 블록 바깥에서 접근 불가

### let

- 블록 스코프를 갖는 지역 변수 선언

```jsx
// 재할당 가능
let number = 10
number = 20

// 재선언 불가능
let number = 10
let number = 20
```

### const

- 블록 스코프를 갖는 지역 변수 선언

```jsx
// 재할당 불가능
const number = 10
number = 10

// 재선언 불가능
const number = 10
const number = 20

// 빈 값으로 선언 불가능
const number
```

- const를 기본으로 사용
    - 재할당이 안 됨을 명확히 표현 → 변수의 용도와 동작 이해를 도와줌
    - 의도치 않은 변수 값 변경으로 인한 버그 예방
    - 재할당이 필요한 경우 let을 쓰면 변경될 수 있음을 명확히 나타냄 → 코드의 유연성 확보

### 변수명 Naming

- 카멜 케이스(camelCase)
    - 변수, 객체, 함수에 사용
- 파스칼 케이스(PascalCase)
    - 클래스, 생성자에 사용
- 대문자 스네이크 케이스(SNAKE_CASE)
    - 상수(constants)에 사용

## 2. DOM

- 웹 페이지(Document)를 구조화된 객체로 제공 → 프로그래밍 언어가 페이지 구조에 접근 가능
    - 문서 구조, 스타일, 내용 등을 변경할 수 있도록 함
- **DOM API:** 다른 프로그래밍 언어가 접근, 조작할 수 있게 페이지 요소들을 객체 형태로 제공


- Node: DOM의 기본 구성 단위 - 트리의 각 부분은 Node라는 객체로 표현됨

### 1. 조작할 요소 선택

```jsx
// 단일 선택
// 제공한 선택자를 만족하는 첫 번째 element 객체 반환(없으면 null 반환)
// CSS 선택자 사용(클래스 선택자 등등)
document.querySelector('selector')

// 다중 선택
// 제공한 선택자를 만족하는 NodeList 반환
// CSS 선택자 사용(클래스 선택자 등등)
document.querySelectorAll('selector')

// console에 찍어보기

```

### 2. 선택된 요소의 컨텐츠 또는 속성을 조작

1. 속성 조작
- ‘classList’ 속성
    - 요소의 클래스 목록을 DOMTokenList(유사 배열)로 반환
- **classList 메서드(기억해두기)**

```jsx
// 지정한 클래스 목록에 클래스 추가, 삭제
element.classList.add('속성 이름')
element.classList.remove('속성 이름')

// 클래스가 존재한다면 제거 -> false 반환, 존재하지 않으면 추가 -> true 반환
element.classList.toggle('속성 이름')
```

- 일반 속성 조작 메서드

```jsx
// 해당 요소에 지정된 값 반환, 없으며 null 반환
element.getAttribute('속성 이름')

// 지정된 요소의 속성 값 설정, 이미 있으면 갱신
element.setgetAttribute('속성 이름', 'value')

// 요소에서 지정된 이름을 가진 속성 제거(ex. id, 등등)
element.removeAttribute('속성 이름')
```

1. HTML 컨텐츠 조작

```jsx
const h1Tag = document.querySelector('selector')
h1Tag.textContent = '내용 수정'
```

1. DOM 요소 조작

```jsx
// 작성한 태그 이름의 HTML요소를 생성하여 반환
const h1Tag = document.createElement('tagName')
h1Tag.textContent = '제목'    // 조회한 태그의 컨텐츠의 값을 변경

// 한 Node를 부모 Node의 자식 NodeList 중 마지막 자식으로 삽입 -> 추가된 Node객체 반환
const divTag = document.querySelector('tagName')
divTag.appendChild(h1Tag)

// DOM에서 자식 Node 제거 -> 제거된 Node 반환
const pTag = document.querySelector('tagName')
divTag.removeChild(pTag)
```

1. 스타일 조작

```jsx
const pTag = document.querySelector('p')
pTag.style.color = 'crimson'
pTag.style.fontSize = '2rem'
pTag.style.border = '2px solid black'
```