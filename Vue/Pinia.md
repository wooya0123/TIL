# State Management Library(Pinia)

- 여러 컴포넌트가 상태를 공유할 때 props와 emit으로 계속 전달해야 함 → 중앙 저장소 사용
- 모든 데이터를 state에 넣는 건 X
    - props, emit 함께 사용해서 앱을 구성
- 앱이 단순하다면 pinia가 없는 게 더 효율적일 수 있음

## 1. 구성요소

### 1. store

- 중앙 저장소
- 모든 컴포넌트가 공유하는 상태, 기능 등이 작성됨
- defineStore() 반환 값 이름은 앞 뒤로 use와 store를 사용할 것을 권장
- 첫 번째 인자는 앱 전체가 사용하는 store의 고유 id

### 2. state

- 반응형 상태(데이터)
- ref( )를 뜻함
- 각 컴포넌트에서는 중앙 저장소에 새로운 상태를 생성 불가(조회만 가능)

### 3.  getters

- 계산된 값
- computed( )를 뜻함
- 컴포넌트에서 조회 가능

### 4. actions

- 메서드
- function( )를 뜻함
- setup stores 반환값
    - pinia의 상태들을 사용하려면 반드시 반환해야 함
    - store에는 private한 상태 속성 X
- state조작, 비동기, API 호출 등 다른 로직 진행 가능
    - 각 컴포넌트에서 조작 x → action에서 로직 설계한 후 사용하기

### 5. plugin

- 앱 상태 관리에 필요한 추가 기능 제공, 확장

## 2. CRUD(Todo 리스트)

### 1. Create

```jsx
// counter.js에 액션 추가
export const useCounterStore = defineStore('counter', () => {
	let id = 0
	  const todos = ref([])
	
	  const addTodo = function (todoText) {
	    todos.value.push({
	      id: id++,
	      text: todoText,
	      isDone: false,
	    })}
	return { todos, addTodo }
}

-------------------------------------------------------------------------------------
// TodoForm.vue에 액션 연결
<template>
    <div>
        <form @submit.prevent="createTodo(todoText)" ref="formElem">  // ref로 DOM 직접 선택
            <input type="text" v-model="todoText">
            <input type="submit">
        </form>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import { useCounterStore } from '@/stores/counter';

const todoText = ref('')                // input에 작성한 텍스트와 양방향 바인딩
const store = useCounterStore()         // pinia 객체 선언

// form 태그 선택
const formElem = ref(null)

// 중앙 저장소에 액션을 직접 호출해도 되지만 액션 전후로 추가 로직 작성하기 위해 메서드 생성
const createTodo = function (todoText) {
    store.addTodo(todoText)          // pinia 객체의 액션 사용
    formElem.value.reset()           // form 태그의 내용을 초기화
}
</script>
```

### 2. Read

```jsx
// TodoList에 작성
<template>
    <div>
        <TodoListItem 
            v-for="todo in store.todos"     // 객체의 todos 속성에 값이 들어있음
            :key="todo.id"
            :todo="todo"                    // props로 값 전달
        />
    </div>
</template>

// script
import TodoListItem from '@/components/TodoListItem.vue';
import { useCounterStore } from '@/stores/counter';

const store = useCounterStore()
```

```jsx
// TodoListItem에 작성
<template>
    <div>
        {{ todo.text }}
		</div>
</template>

// script
defineProps({
    todo: Object
})
```

### 3. Update

```jsx
// counter.js에 액션 추가
// 전달 받은 id로 todo 목록에서 찾고 isDone 속성값을 반대로 재할당 후 새로운 todo 목록 반환
const updateTodo = function (selectedId) {
  todos.value = todos.value.map((todo) => {
    if (todo.id === selectedId) {
      todo.isDone = !todo.isDone
    }
    return todo
  })
}
```

```jsx
// TodoListItem에 isDone에 따라서 상태 변경
<template>
    <div>
        <span 
            @click="store.updateTodo(todo.id)"
        >
    </div>
</template>
```

### 4. Delete

```jsx
// counter.js에 액션 추가
const deleteTodo = function (selectedId) {
  const index = todos.value.findIndex((todo) => todo.id === selectedId)
  todos.value.splice(index, 1)
}
```

```jsx
// TodoListItem에 버튼 추가
<template>
    <div>
				<button @click="store.deleteTodo(todo.id)">삭제</button>
    </div>
</template>

// script에 작성
import { useCounterStore } from '@/stores/counter';

const store = useCounterStore()
```

## 3. Local Storage

- 브라우저가 갖고 있는 저장공간
- 페이지를 새로 고침하고 브라우저 다시 실행해도 데이터 유지
- 네트워크 요청 시 서버로 전송 X (쿠키와 다름)
- 여러 탭이나 창 간에 데이터 공유
- 사용자 설정, 상태정보, 캐시 데이터 등을 클라이언트 측에 보관 → 웹 성능 향상, 사용자 경험 개선

### pinia-plugin-persistedstate

- 웹 앱의 상태를 local storage나 session storage에 영구적으로 저장, 복원