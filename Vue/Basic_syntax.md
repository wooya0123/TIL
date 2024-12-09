### SPA(Single Page Application)

- 단일 페이지에서 동작하는 웹 애플리케이션
1. 최초 로드 시 필요한 모든 리소스 다운로드
2. 페이지 갱신에 필요한 데이터만 비동기적으로 받아 동적으로 갱신

### CSR(Client-side Rendering)

1. 사용자가 웹사이트에 요청
2. 서버는 최소한의 HTML과 JS 파일을 클라이언트로 전송
3. 클라이언트는 HTML과 JS를 다운로드
4. 브라우저가 JS를 실행해 동적으로 페이지 컨텐츠 생성
5. 필요한 데이터는 API를 통해 서버로부터 비동기적으로 가져옴

### 장점&단점

- 장점
    1. 빠른 페이지 전환
        - 서버로 전송되는 데이터 양 최소화(서버 부하 방지)
    2. 네이티브 앱과 유사한 사용자 경험 제공
    3. Frontend와 Backend의 명확한 분리
- 단점
    1. 느린 초기 로드 속도
    2. SEO(검색 엔진 최적화) 문제

### 핵심 기능

1. 선언적 렌더링
    - Vue 템플릿 구문 → JS 상태(데이터) 기반으로 화면에 출력될 HTML 선언적으로 작성
2. 반응성
    - JS 상태 변경을 추적하고 변경사항 발생 → DOM 자동 업데이트

---

### Component(=재사용 가능한 코드 블럭)

- UI를 독립적으로 재사용 가능한 일부분을 분할 → 개별적으로 다룰 수 있음
- 애플리케이션은 중접된 component의 트리 형태로 구성

## 1. Vue Application 생성

```jsx
// CDN 설치
<div id="app">
  <h1>{{ message }}</h1>
  
  <button v-on:click="increment">
      count: {{ number }}
  </button>
</div>

<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
<script>
  // 구조분해할당으로 Vue 객체의 createApp함수 할당
  const { createApp, ref } = Vue

	// createApp 함수로 Application instance 생성 -> 객체 전달
	// 모든 App에는 하위 컴포넌트로 포함할 수 있는 루트 컴포넌트 필요
  const app = createApp({
    setup() {
      const message = ref('hello')      // ref 객체 반환(.value 속성 존재)
	    const number = ref(0)
	    const increment = function () {
		    number.value++     // ref 객체의 값을 조작하려면 .value를 사용
	    
	    return {             // 템플릿 문법을 사용하려면 return 해줘야 함
		    message,
		    increment,
	    }
	   }
  })

	// HTML 요소에 Vue 앱 인스턴스 연결(각 앱 인스턴스에 한 번만 호출 가능)
  app.mount('#app')     
</script>
```

- **ref()**
    - .value 속성이 있는 ref 객체로 wrapping하여 반환하는 함수
        - 반응형을 가지는 참조형 변수를 만드는 것
        - 템플릿에서 ref 사용할 때는 .value를 작성할 필요 X
            - setup() 함수 내에서 선언 → ref 객체를 return을 해줘야 함
        - ref 값이 변경되면 자동으로 DOM을 업데이트
            - 렌더링 중에 사용된 모든 ref를 추적 → 변경되면 구성요소에 대해 다시 렌더링
    - 모든 타입 자료형 사용 가능
    - 템플릿에서 unwrap은 최상위 속성인 경우에만 적용 가능
    - ref 객체를 최상위 속성으로 해야 unwrap 가능

## 2. 템플릿 문법

- DOM을 기본 구성 요소 인스턴스의 데이터에 선언적으로 바인딩
    - Vue 인스턴스와 DOM 연결
- 표현식은 return 뒤에 사용할 수 있는 코드여야 함(선언식은 사용 불가)
    - `{{ const number = 1 }}` → 이렇게는 안 됨
- 템플릿 문법 안에 제어문을 사용하려면 삼항 표현식 사용
    - `{{ if (ok) {return message} }}`

1. Text Interpolation
    - 이중 중괄호 구문(콧수염 구문)
    - 속성이 변경될 때마다 업데이트 됨
    - 데이터를 일반 텍스트로 해석
2. Raw HTML
    - 실제 HTML을 출력하려면 v-html 사용
    
    ```jsx
    <div v-html="rawHtml"></div>
    
    const rawHtml = ref('<span style="color:red">this should be red.</span>')
    ```
    
3. Attribute Bindings
    - 콧수염 구문은 HTML 속성 내에서 사용 불가 → v-bind 사용
    - 바인딩 값이 null이나 undefined인 경우 랜더링 요소에서 제거
    
    ```jsx
    <div v-bind:id="dynamicId"></div>
    
    const dynamicId = ref('my-id')
    ```
    
4. JavaScript Expressions
    - 모든 데이터 바인딩 내에서 JavaScript 표현식의 모든 기능을 지원
    - Vue 템플릿에서 JavaScript 표현식을 사용할 수 있는 위치
        1. 콧수염 구문 내부
        2. 모든 directive의 속성 값(’v-’로 시작하는 특수 속성)
    - 각 바인딩에는 하나의 단일 표현식만 포함 가능
        - 표현식: 값으로 평가할 수 있는 코드 조각(return 뒤에 사용할 수 있는 것)

## 3. Directive 특징

- ‘v-’형태 → directive의 속성 값은 단일 JS 표현식이어야 함
- 표현식 값이 변경될 때 DOM에 반응적으로 업데이트

- Argument
    - directive에 인자를 넣는 경우가 있음(ex. v-bind, v-on)
- Modifiers
    - directive가 특별한 방식으로 바인딩 됨을 나타냄(.prevent → event.preventDefault()를 호출)

### v-bind

1. 속성 바인딩
- HTML 속성 값을 Vue의 상태 속성 값과 동기화
- shorthand 사용(‘ : ’)

```jsx
// 둘은 같은 코드
<img v-bind:src="imageSrc">
<img :src="imageSrc">

const imageSrc = ref('https://picsum.photos/200')
```

- 동적 인자 이름
    - [ ]로 감싸서 사용
    - [ ]안에 작성하는 이름은 반드시 소문자로만 구성

```jsx
<p :[dynamicattr]="dynamicValue">Dynamic Attr</p>

const dynamicattr = ref('title')
const dynamicValue = ref('Hello Vue.js')
```

1. 클래스 바인딩
- 객체 활용
    - `:class="{ 'active': isActive }`
        - 객체의 value값이 true면 key값을 class에 넣기, false면 제거

```jsx
// Inline 스타일(HTML 태그에서 속성을 객체로 작성)
// isActive가 true면 'active'클래스 부여, false면 클래스 제거
<div :class="{ 'active': isActive }">Text</div>
<div class="static" :class="{ active: isActive, 'text-primary': hasInfo }">Text</div>

// 반응형 변수 활용(변수 선언 때 객체로 묶어서 전달)
<div class="static" :class="classObj">Text</div>

const isActive = ref(true)
const hasInfo = ref(true)
const classObj = ref({
  active: isActive,
  'text-primary': hasInfo,
})
```

- 배열 활용

```jsx
// 배열 안에 변수들을 넣어서 전달
<div :class="[activeClass, infoClass]">Text</div>
// 배열 안에 객체를 바로 작성도 가능
<div :class="[{ active: isActive}, infoClass]">Text</div>

const activeClass = ref('active')
const infoClass = ref('text-primary')
```

1. 스타일 바인딩
- 객체 활용

```jsx
// style 속성에 적용(JS 스타일)
<div :style="{ color: activeColor, fontSize: fontSize + 'px'}">Text</div>

// JS 스타일의 속성명을 모르겠다면 문자열로 CSS 스타일로 작성해도 됨
<div :style="{ color: activeColor, 'font-Size': fontSize + 'px'}">Text</div>

// 객체로 전달해도 됨
<div :style="styleObj">Text</div>

const activeColor = ref('crimson')
const fontSize = ref(50)
const styleObj = ref({
  color: activeColor,
  fontSize: fontSize.value + 'px',
})
```

- 배열 활용

```jsx
// 겹치는 속성이 있으면 마지막 거로 병합됨
<div :style="[styleObj, styleObj2]">Text</div>

const styleObj = ref({
  color: activeColor,
  fontSize: fontSize.value + 'px',
})
const styleObj2 = ref({
  color: 'blue',
  border: '1px solid black',

})
```

### v-on

- DOM 요소에 이벤트 리스너를 연결 및 수신
- shorthand(@)

```jsx
// inline에 바로 작성해도 되고 메서드를 사용해도 됨(메서드를 더 권장)
<button v-on:click="count++">Add 1</button>
<button @click="increase">Hello</button>
<p>Count: {{ count }}</p>

const count = ref(0)
const increase = function () {
  count.value += 1
}
```

- inline 핸들러에서 인자 받기

```jsx
// 인자를 받아서 사용 가능
<button @click="greeting('hello')">Say hello</button>

// 이벤트가 발생했을 때 사용할 수 있는 event객체도 인자를 받을 수 있음
// 앞에 $ 표시가 있으면 직접 만든 변수가 아닌 기본 global 변수임을 나타냄
// event 인자의 위치는 개발자 마음대로 지정 가능, 인자를 안 넣으면 자동으로 event 객체를 받음
<button @click="warning('경고입니다', $event)">Submit</button>

const greeting = function (message) {
  console.log(message)
}
const warning = function (message, event) {
  console.log(message)
  console.log(event)
}
```

- event modifiers
    - 이벤트가 발생할 때 추가 옵션
    - .~~로 계속 이어서 작성 가능

```jsx
// event 발생할 때 preventDefault() 구문을 메서드에 안 적어도 됨
<form @submit.prevent="onSubmit">
  <input type="submit">
</form>

const onSubmit = function (event) {
  console.log('onSubmit')
}
```

- key modifiers
    - 해당 키를 누르면 작동

```jsx
// 쓸 수 있는 속성값 공식 문서에서 찾아서 사용
<input @keyup.enter="onSubmit">

const onLink = ref('https://unpkg.com/vue@3/dist/vue.global.js')
```

### 양방향 바인딩

1. v-bind, v-on 같이 사용

```jsx
<p>{{ inputText1 }}</p>

// input의 value는 inputText1을 사용하고 input 이벤트가 발생할 때마다 onInput 메서드 실행
<input :value="inputText1" @input="onInput">

const inputText1 = ref('')
const onInput = function (event) {
  inputText1.value = event.currentTarget.value
}
```

1. v-model

```jsx
<p>{{ inputText2 }}</p>

// 위 구조를 v-model이 구현
// 문자 하나가 완성되면 보여줌 -> 비영어권 언어들은 v-bind, v-on을 활용하기
<input v-model="inputText2">

const inputText2 = ref('')
```

- Checkbox, Radio, Select 등 다양한 타입의 입력과 함께 사용

```jsx
// check 박스에서 체크됐는지 바로 확인
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>

// 체크된 값이 배열에 체크된 순서대로 들어감
<div>Checked names: {{ checkedNames }}
<input type="checkbox" id="alice" value="Alice" v-model="checkedNames">
<label for="alice">Alice</label>

// 드롭다운에서 선택한 값이 나타남
<div>Selected: {{ selected }}</div>
<input type="checkbox" id="bella" value="Bella" v-model="selected">
<label for="bella">Bella</label>

const checked = ref(false)
const checkedNames = ref([])
const selected = ref('')
```

## 4. Computed Properties

- 미리 계산된 속성을 사용해 템플릿에서 표현식을 단순하게, 불필요한 반복 연산 줄임
- 반환되는 값은 computed ref 객체
- computed 속성은 의존된 반응형 데이터를 자동으로 추적
- 의존하는 데이터가 변경될 때만 재평가
- 읽기 전용이므로 값을 변경하려고 하면 안 됨

```jsx
const { createApp, ref, computed } = Vue

const restOfTodos = computed(() => {
  // return numbers.reverse()       // 원본 변경 금지
  return [...numbers].reverse()     // 배열을 복사해서 return 
})
```

### computed와 method 차이

- computed는 의존된 반응형 데이터 기반으로 캐시됨
    - 의존된 반응형 데이터가 변경되지 않는 한 계산된 결과를 즉시 반환
    - 의존하는 데이터에 따라 결과가 바뀌는 계산된 속성 만들 때 유용
- method는 다시 렌더링이 발생할 때마다 함수 실행
    - 데이터 의존 여부와 관계 없이 특정 동작을 수행하는 함수 정의할 때 사용

## 5. 조건부 렌더링

### v-if

- template 태그
    - 페이지가 로드될 때 렌더링 되지 않지만 JS를 사용해 문서에서 사용할 수 있도록 하는 HTML을 보유하기 위한 메커니즘

```jsx
<div v-if="name === 'Alice'">Alice입니다</div>
<div v-else-if="name === 'Bella'">Bella입니다</div>
<div v-else>아무도 아닙니다.</div>

// template 태그로 감싸서 여러 요소에 한 번에 적용
<template>
  <div>Cathy입니다</div>
  <div>나이는 30살입니다</div>
</template>
```

### v-show

- 표현식 값의 true, false를 기반으로 요소의 가시성을 전환
- v-show 요소는 항상 DOM에 렌더링
    - CSS display 속성만 none으로 전환함

### v-if  vs  v-show

- v-if는 초기 조건이 false인 경우 작업 수행 X
    - 토글 비용이 높음
- v-show는 초기 조건에 상관없이 항상 렌더링
    - 초기 렌더링 비용이 높음

→ 컨텐츠를 자주 전환(v-show), 실행 중에 조건이 변경되지 않는 경우(v-if)

## 6. 리스트 렌더링

### v-for

- 중첩 for문도 가능

```jsx
// 받는 인자 순서 유의하기
<div v-for="(value, key, index) in myArr">
  {{ value }} / {{ key }} / {{ index }}
</div>

// template 태그로 여러 요소를 반복 렌더링
<template v-for="item in myArr">
  <li>{{ item.name }}</li>
  <li>{{ item.age }}</li>
  <hr>
</template>
```

- 반드시 key와 함께 사용하기 (우선 순위 A)
    - 내부 컴포넌트의 상태를 일관되게 하여 데이터의 예측 가능한 행동을 유지
    - key 속성은 Vue의 내부 Virtual DOM 알고리즘이 노드를 식별하는 데 필수적인 힌트를 제공
    - 에러가 발생하지 않더라도 key 속성을 사용하는 것이 Vue 애플리케이션의 안정성과 성능을 위해 매우 중요
    1. 권장되는 key 값
        - 데이터베이스의 고유 ID
        - 항목의 고유한 식별자
        - 변경되지 않는 속성 값
    2. 피해야 할 사항
        - 배열의 인덱스를 key로 사용하는 것
        - 객체를 key로 사용하는 것

```jsx
<div v-for="item in items" :key="item.id">
  {{ item.name }}
</div>
```

### v-for  vs  v-if (우선 순위 A)

- 동일한 요소에서 v-if가 v-for보다 우선순위가 더 높음

<해결책>

```jsx
<!-- 해결책 1. computed를 활용해 이미 필터링 된 목록을 반환하여 반복 -->
<ul>
  <li v-for="todo in completeTodos" :key="todo.id">
    {{ todo.name }}
  </li>
</ul>

// 미리 계산해서 저장
const completeTodos = computed(() => {
  return todos.value.filter((todo) => !todo.isComplete)
})

<!-- 해결책 2. template 요소를 사용하여 v-for와 v-if의 위치를 분리 -->
<ul>
  <template v-for="todo in todos" :key="todo.id">
    <li v-if="!todo.isComplete">
      {{ todo.name }}
    </li>
  </template>
</ul>
```

### v-for & 배열로 필터링, 정렬

- 원본 배열 변화 method
    - push(), pop(), shift(), unshift(), splice(), sort(), reverse()
- 새 배열을 반환 method
    - filter(), concat(), slice()

---

1. computed 활용
    - 미리 계산해둔 값을 순회
2. method 활용(중첩된 for문일 때)
    - 큰 배열을 순회 → 작은 배열에서는 method로 원하는 값 출력

## 6. Watch

- 하나 이상의 반응형 데이터를 감시 → 감시하는 데이터가 변경되면 콜백 함수 호출
    - 배열을 활용해 여러 대상을 감시 가능
- 첫 번째 인자(source)
    - watch가 감시하는 대상(반응형 변수, 값을 반환하는 함수 등)
- 두 번째 인자(callback function)
    1. new value(필수)
        - 감시하는 대상이 변화된 값
    2. old value(옵션)
        - 감시하는 대상의 기존 값

```jsx
<input v-model="message">
<p>Message length: {{ messageLength }}</p>

const { createApp, ref, watch } = Vue

const message = ref('')
const messageLength = ref(0)

watch(message, (newValue) => {                 // watch는 const 안 씀
  messageLength.value = newValue.length
})
```

```jsx
// 여러 대상 감시하려면 배열 활용
// 인자의 순서를 맞춰줘야 함
watch([one, two], ([newOne, newTwo], [prevOne, prevTwo]) => {
})
```

- 공통점
    1. 데이터 변화를 감지하고 처리
    2. 의존(감시)하는 원본 데이터를 직접 변경하지 않음

|  | Computed | Watchers |
| --- | --- | --- |
| 동작 | 의존하는 데이터 속성의 계산된 값을 return | 특정 데이터 속성의 변화를 감시하고 작업 |
| 사용 목적 | 계산된 값을 캐싱하여 재사용, 중복 계산 방지 | 데이터 변화에 따른 특정 작업 수행 |
| 사용 예시 | 연산된 길이, 필터링 된 목록 계산 | DOM 변경, 다른 비동기 작업, 외부 API 연동 |
- Deep Watchers

## 7. Lifecycle Hooks

- Vue 컴포넌트의 생성부터 소멸까지 각 단계에서 실행되는 함수
- 호출 시점을 컨트롤 할 수 없음
- 자주 쓰는 종류
```
// setup: Vue 라이프사이클
// Setup: 최초 세팅
// Mount: DOM 이 그려진 후
// Update: 화면 완성 후 데이터 변경 시
// UnMount: 다른 컴포넌트로 이동 시
```

```jsx
const { createApp, ref, onMounted, onUpdated, onUnmounted } = Vue

// app이 마운트될 때 바로 실행
onMounted(() => {
  console.log('mounted')
})

// 값이 업데이트될 때 실행
onUpdated (() => {
  console.log('updated')
})
```