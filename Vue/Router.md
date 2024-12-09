# Vue Router

### Routing

- 네트워크에서 경로를 선택하는 프로세스
    - 웹 앱에서 다른 페이지 간의 전환과 경로를 관리
- SPA에서 routing이 없으면
    - 유저는 URL을 통한 페이지 변화 감지 X
    - 페이지가 무엇을 렌더링 중인지 상태를 알 수 없음
        - url이 1개라 새로 고침 시 처음 페이지로 돌아감
        - 링크 공유할 때 첫 페이지만 공유 가능
    - 브라우저의 뒤로 가기 기능 사용 불가
    
    → 페이지는 1개지만 주소 따라 여러 컴포넌트를 새로 렌더링해서 여러 페이지인 것처럼
    

## 1. Vue 프로젝트 변화

### App.vue 변화

1. RouterLink
    - 페이지를 다시 로드하지 않고 url을 변경
    - HTML의 a태그를 렌더링
2. RouterView
    - RouterLink의 url에 해당하는 컴포넌트를 표시

### Router 폴더 생성

- 라우팅에 관련된 정보 및 설정이 작성됨
- router에 url과 컴포넌트를 매핑

### Views 폴더 생성

- RouterView 위치에 렌더링할 컴포넌트 배치
- 기존 components 폴더와 기능적으로 다른 건 없음 (단순 분류의 의미)
- 일반 컴포넌트와 구분하기 위해 컴포넌트 이름을 View로 끝나게 작성하는 것을 권장
    - 라우트링크랑 직접적으로 연결되는 컴포넌트는 여기에 작성하는 것을 권장

## 2. 라우팅 기본

1. index.js에 라우터 관련 설정 작성 (주소, 이름, 컴포넌트)
    - 동적 경로 매칭
    
    ```jsx
    // index.js에 추가
    {
      path: '/user',         
      name: 'user',
      component: UserView,       // 연결할 컴포넌트
    }
    
    // view 컴포넌트 생성, 라우터링크의 파라미터 사용(2가지 방식)
    <template>
        <div>
            <h1>UserView</h1>
            <h2>{{ $route.params.id }}번 유저 프로필</h2>   // route 안에 parmas 속성 사용
            <h2>{{ userId }}번 유저 프로필</h2>        // 템플릿을 깔끔하게 사용하기 위해
        </div>
    </template>
    
    <script setup>
    import { useRoute } from 'vue-router';
    import { ref } from 'vue';
    
    const route = useRoute()                  // $route와 같은 기능
    const userId = ref(route.params.id)       
    </script>
    ```
    
2. RouterLink의 to 속성으로 index.js에서 정의한 주소값 사용
    - index.js에서 설정한 name을 사용 → RouterLink에 v-bind로 ‘to’ props 객체를 전달 가능
    
    ```jsx
    // Vue 컴포넌트에서 라우터링크 추가
    // 객체 안에 params 속성 추가 가능 -> index.js에서 지정한 매개변수 이름과 같아야 함
    <template>
    <RouterLink :to="{ name: 'user', params: { id: userId } }">User</RouterLink>
    
    <RouterView />
    </template>
    
    <script setup>
    import { ref } from 'vue'
    const userId = ref(1)       // params 설정(나중에는 서버에서 받은 값 사용)
    </script>
    ```
    
3. RouterLink 클릭 시 RouterView에 렌더링

## 3. 중첩된 라우팅

- 부모 - 자식 관점 X, 중첩된 관계를 표현하는 관점
1. Components 폴더에 컴포넌트 생성
2. 라우터 등록

```jsx
// index.js에 두 컴포넌트 import
import UserPosts from '@/components/UserPosts.vue'
import UserProfile from '@/components/UserProfile.vue'

// children 옵션 사용(보통 하위 경로에만 이름을 지정)
{
  path: '/user/:id',     // Dynamic Route Matching 사용
  component: UserView,
  children: [
    { path: '', name: 'user', component: UserProfile},     // 렌더링 될 때 기본 페이지
    { path: 'profile', name: 'user-profile', component: UserProfile},
    { path: 'posts', name: 'user-posts', component: UserPosts}
  ],
},
```

1. 라우터링크 작성

```jsx
<template>
    <div>
        <h1>UserView</h1>
        <RouterLink :to="{ name: 'user-profile'}">Profile</RouterLink>
        <RouterLink :to="{ name: 'user-posts'}">Posts</RouterLink>
        
        <RouterView/>
    </div>
</template>
```

## 4. Programmatic Navigation

- 라우터링크 대신 JS를 사용해 페이지 이동
- router의 인스턴스 메서드를 사용해 라우터링크를 a태그를 만드는 것처럼

### 1. router.push()

- 다른 url로 이동하는 메서드
    - 라우터링크를 클릭했을 때 내부적으로 호출되는 메서드(라우터 링크 클릭 == router.push())
    - 선언적 표현: `<RouterLink :to="...">`
    - 프로그래밍적 표현: `router.push(<객체>)`
- 새 항목을 브라우저의 history stack에 push하고 뒤로 가기 하면 stack에서 이전 주소 꺼내옴

```jsx
// 이동하려는 페이지에서 작성
<template>
    <div>
			<button @click="gohome">홈으로</button>
    </div>
</template>

<script setup>
import { useRoute, useRouter } from 'vue-router'

const router = useRouter()
const gohome = function () {
    router.push({ name: 'home' })
}
</script>
```

### 2. router.replace()

- history stack에 새로운 항목을 push하지 않고 다른 url로 이동(뒤로가기 불가)
    - 선언적 표현: `<RouterLink :to="..." replace>`
    - 프로그래밍적 표현: `router.replace(<객체>)`

## 5. Navigation Guard

- Vue 라우터를 통해 특정 url에 접근할 때 다른 url로 redirect 또는 취소하여 네비게이션 보호
    - 라우트 전환 전/후 자동으로 실행되는 Hook
- 모든 가드는 2개의 인자를 받음(to, from)

### 1. Globally

- 앱 전역에서 모든 라우트 전환에 적용되는 가드
- 작성위치: index.js(맨 아래 작성 추천)
- router.beforeEach()
    1. return false
        - 현재 내비게이션 취소
        - 사용자가 수동으로 or 뒤로가기 했으면 from 경로의 url로 재설정
    2. 라우트 주소
        - `return { name: ‘About’ }`
        - router.push() 호출하는 것처럼 경로 위치를 전달 → 다른 위치로 redirect
        - return이 없으면 자동으로 to url 라우트 객체로 이동
    
    ```jsx
    router.beforeEach((to, from) => {
      const isAuthenticated = false
    
      // 로그인이 되어있지 않고, 이동하려는 페이지가 로그인 페이지가 아니면
      if (!isAuthenticated && to.name !== 'login') {
        console.log('로그인이 필요합니다')
        alert('로그인이 필요합니다')
        return { name: 'login' }
      }
    })
    ```
    

### 2. Per-route

- 특정 라우트에만 적용되는 가드
- 작성위치: index.js의 각 routes
- router.beforeEnter()
    - 특정 라우트에 진입했을 때만 실행되는 함수
    - 매개변수나 쿼리 값이 변경될 때는 실행 X, 다른 url에서 탐색해 올 때만 실행
    
    ```jsx
    {
      path: '/',
      name: 'login',
      component: LoginView,
      beforeEnter: (to, from) => {
        if (isAuthenticated === true) {
          console.log('이미 로그인 상태입니다')
          return { name: 'home' }
        }
      }
    }
    ```
    

### 3. In-component

- 컴포넌트 내에서만 적용되는 가드
- 작성위치: 각 컴포넌트의 script 내부
1. onBeforeRouteLeave()
    - 현재 라우트에서 다른 라우트로 이동하기 전 실행
    
    ```jsx
    onBeforeRouteUpdate((to, from) => {
        userId.value = to.params.id
        }
    )
    ```
    
2. onBeforeRouteUpdate()
    - 이미 렌더링 된 컴포넌트가 같은 라우트 내에서 업데이트 되기 전 실행