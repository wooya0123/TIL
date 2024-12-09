# Single-File Components

- Component : 재사용 가능한 코드 블록
    - 앱은 중첩된 component의 트리 형태가 됨

### Single-File Component(SFC)

- component의 template, script, style을 하나의 파일로 묶어낸 특수한 파일 (.vue)
- template
    - 각 .vue 파일은 template 블록 하나만 가능
- script setup
    - 컴포넌트의 setup() 함수 역할
    - 각 .vue 파일은 script setup 블록 하나만 가능
        - 일반 script는 추가 가능
    - return 안 해도 자동으로 사용 가능
- style scoped
    - 각 .vue 파일은 여러 개의 style 태그 포함 가능
    - scoped가 적용되면 CSS는 현재 컴포넌트에만 적용

### Build

- 프로젝트의 소스 코드를 최적화하고 번들링하여 배포할 수 있는 형식으로 변환하는 과정
- 개발 중에 사용되는 여러 소스 파일 및 리소스를 최적화된 형태로 조합

## 1. Vue Project 생성

```bash
npm create vue@latest

cd vue-project
npm install
npm run dev
```

### node.js

- Chrome의 V8 JavaScript 엔진을 기반으로 하는 Server-Side 실행 환경
- JS를 브라우저가 아닌 서버 측에서도 실행할 수 있게 함
    - 프론트와 백에서 동일한 언어로 개발할 수 있게 됨
- 오픈 소스 패키지와 라이브러리를 제공

### Module & Bundler

- 프로그램을 구성하는 독립적인 코드 블록(*.js 파일)
- 앱이 크기가 커짐 → 파일을 여러 개로 분리하여 관리 → 분리한 각 파일이 모듈
    - 모듈도 너무 커져서 모듈 간 의존성 문제 → Bundler
    - 번들러로 여러 모듈과 파일을 하나의 번들로 묶어 최적화

### Vue Project 구성요소

1. node_modules
    - 외부 패키지들이 저장 → 프로젝트가 실행될 때 필요한 라이브러리, 패키지 포함
    - 프로젝트의 의존성 모듈을 저장하고 관리하는 공간
    - .gitignore에 작성됨
2. package-lock.json
    - 패키지들의 실제 설치 버전, 의존성 관계 등 패키지 설치에 필요한 모든 정보 포함
    - package.json은 패키지의 범위를 적어줌
        - ^ → 이걸로 범위를 나타냄
    - package-lock.json은 특정 버전을 적어줌
3. public
    - 정적 파일을 위치
        - 소스코드에서 참조되지 않는, 항상 같은 이름을 갖는, import가 필요 없는
    - 항상 절대 경로를 사용하여 참조
4. src
    - 프로젝트 주요 소스 코드를 포함
    - 컴포넌트, 스타일, 라우팅 등 프로젝트 핵심 코드 관리
    - /assets
        - 프로젝트 내 사용되는 자원(이미지, 폰트, 스타일 시트 등)
        - 컴포넌트 자체에서 참조하는 내부 파일 저장
        - 컴포넌트가 아닌 곳에서는 public 사용
    - /components
        - Vue 컴포넌트들을 작성
    - /App.vue
        - Vue 앱의 최상위 루트 컴포넌트
        - 다른 하위 컴포넌트들을 포함
    - /main.js
        - Vue 인스턴스 생성, 앱 초기화하는 역할
        - 필요한 라이브러리를 import하고 전역 설정을 수행
5. index.html
    - Vue 앱의 기본 HTML 파일
    - App.vue가 해당 페이지에 mount 됨 (하나의 페이지)
    - 필요한 스타일시트, 스크립트 등의 외부 리소스를 로드
6. jsconfig.json(설정할 일 없음)
    - 컴파일 옵션, 모듈 시스템 등 설정
7. vite.config.js(설정할 일 없음)
    - Vite 프로젝트 설정 파일
    - 플러그인, 빌드 옵션, 개발 서버 설정 등

## 2. 컴포넌트 생성

1. App.vue 초기화
2. src/components 에 컴포넌트 생성
3. App 컴포넌트에 하위 컴포넌트 등록
    - 최상위 앱 말고 컴포넌트에 하위 컴포넌트도 등록 가능

```jsx
// 컴포넌트에 등록 -> 컴포넌트 구조를 위해 최상단 HTML 요소를 작성해주는 게 좋음
<template>
  <h1>App.Vue</h1>
  <MyComponent />     // 하위 컴포넌트(파스칼 케이스 셀프 닫기)
</template>

<script setup>
// src는 자주 참조하는 경로이므로 절대경로로 지정되어 있음(@)
import MyComponent from '@/components/MyComponent.vue';
</script>

<style scoped>

</style>
```

- CSS 선택자는 클래스 선택자 사용하는 것을 추천
    - 하위 컴포넌트의 최상위 HTML이 현재 컴포넌트의 HTML과 같다면 scoped여도 하위 컴포넌트에도 스타일이 상속되는 일이 발생

### Virtual DOM

- 실제 DOM에 직접 접근하지 말 것
    - JS에서 사용하는 DOM 조작 메서드
- Vue의 ref( )와 Lifecycle Hooks 함수를 사용해 간접적으로 접근하여 조작

```jsx
<template>
    <div>
        <input ref="input">             // template에 input 태그의 ref와 연결
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// 변수명은 템플릿 ref 값과 일치해야 함
const input = ref(null)                

onMounted(() => {
    console.log(input.value)     // <input>
})
</script>
```

### Scaffolding

- 새로운 프로젝트나 모듈을 시작할 때 초기 구조와 기본 코드를 자동으로 생성하는 과정
- 개발자들이 프로젝트를 시작하는 데 도움을 주는 틀이나 기반을 제공하는 작업

## 3. Props

- 부모는 자식에게 데이터를 전달(Pass Props), 자식은 부모에게 이벤트를 알림(Emit Event)
    - 자식 컴포넌트는 props 변경 불가
    - 부모 컴포넌트가 업데이트 되면 자식 컴포넌트의 props 업데이트

### One-Way Data Flow

- 부모-자식 간 하향식 단방향 바인딩
    - 자식 컴포넌트가 부모 컴포넌트의 상태를 변경해 데이터 흐름을 이해하기 어렵게 만드는 것을 방지 → 데이터 흐름의 일관성, 단순화

### Props 선언

1. Props를 내려줄 부모 컴포넌트에서 선언
    
    ```
    // 부모 컴포넌트 template에서 props 선언 (key-value 형태로 전달)
    // template 영역이므로 HTML 문법(케밥 케이스)로 작성
    <template>
    		<ParentChild my-msg="message"/>           // 정적 props
    		<ParentGrandChild :my-msg="message"/>     // 동적 props(변수 값을 넘겨줌)
    </template>
    ```
    
2. 자식 컴포넌트에서 props 받기
    - prop을 객체로 선언하는 것을 권장
        - 가독성
        - 잘못된 유형을 전달할 때 브라우저 콘솔에 경고 출력
        - 유효성 검사로서 활용 가능
    
    ```jsx
    <template>
        <div>
            {{ myMsg }}
        </div>
    </template>
    
    // defineProps 배열 or 객체를 받는 함수를 사용(script 영역이기 때문에 카멜 케이스로 작성)
    <script setup>
    defineProps(['myMsg'])     // 배열 형태
    defineProps({              // 객체 형태(권장)
        myMsg : String,        // prop 이름: 자료형
    })
    </script>
    ```
    

### 디렉티브 + props

```jsx
// 부모 컴포넌트에서 prop 넘겨주기
<template>
	<div>
		<ParentItem 
		    v-for="item in items"
		    :key="item.id"
		    :my-prop="item"
		/>
	<div>
</template>

<script setup>
import ParentItem from '@/components/ParentItem.vue';
const items = ref([
    {id: 1, name: '사과'},
    {id: 2, name: '바나나'},
    {id: 3, name: '딸기'},
])
</script>
-------------------------------------------------------------------------------------
// 자식 prop에서 받기
<template>
    <div>
        {{ myProp.id }}
        {{ myProp.name }}
    </div>
</template>

<script setup>
defineProps({
    myProp: Object,
})
</script>
```

## 4. Emit

1. 자식 컴포넌트에서 이벤트 발신
    - emit 이벤트도 배열, 객체 구문으로 선언 가능
        - 객체로 선언하면 유효성 검사 가능

```jsx
<template>
    <div>
		    <button @click="buttonClick">클릭</button>
		    <button @click="emitArgs">추가 인자 전달</button>
    </div>
</template>

<script setup>
// defineEmits()는 $emit 대신 사용할 수 있는 동등한 함수를 반환(script에서는 $emit 메서드에 접근 불가)
// 인자로 배열, 객체(유효성 검사 필요) 가능
const emit = defineEmits(['someEvent', 'emitArgs',])     
const buttonClick = function () {
    emit('someEvent')
}
const emitArgs = function () {
    emit('emitArgs', 1, 2, 3)     // 부모 컴포넌트로 추가 인자 전달도 가능
}
</script>
```

1. 부모 컴포넌트에서 이벤트 수신
    - template에서는 kebap-case, script에서는 camelCase 사용하기
```jsx
<template>
    <div>
        <ParentChild 
        @some-event="someCallback"     // 이벤트를 수신하면 script에서 정의한 메서드 실행
        @emit-args="getNumbers"
        />
    </div>
</template>

<script setup>
const someCallback = function () {
    console.log('자식 컴포넌트에서 발신한 이벤트를 수신했어요.')
}
const getNumbers = function (...args) {
    console.log(args)
</script>
```