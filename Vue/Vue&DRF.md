## 1. CORS

- 웹 앱이 다른 출처의 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제
    - 원래 웹 브라우저는 같은 출처에서만 요청하는 것을 허용, 다른 출처로의 요청은 차단
- 다른 출처의 리소스를 불러오려면 다른 출처에서 올바른 CORS header를 포함한 응답을 반환해야 함 → 서버에서 CORS header를 만들어야 함

### django-cors-headers

- 손쉽게 응답 객체에 CORS header를 추가해주는 라이브러리

```python
pip install django-cors-headers

# settings.py
INSTALLED_APPS = [
    'corsheaders',
    ...

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    ...
    
CORS_ALLOWED_ORIGINS = [
    'http://127.0.0.1:5173',      # Vue 프로젝트의 도메인 등록
    'http://localhost:5173',
]
```

## 2. 인증

- 인증 요청 → 자격 증명 자료와 연결 → 권한 및 제한 정책 → 요청을 허용할지 결정
- DRF에서는 view함수 시작 시, 권한 및 제한 확인이 발생하기 전, 다른 코드의 진행이 허용되기 전 실행
    - 인증으로는 들어오는 요청을 허용/거부는 불가 → 요청에 사용된 자격 증명만 식별
    - 401 Unauthorized
        - 자격 증명이 없음
    - 403 Forbidden
        - 서버에 요청은 전달 but 권한 때문에 거절
        - 서버는 클라이언트가 누구인지 알고 있음

### 인증 정책 설정 방법 2가지

1. 전역 설정
    - 프로젝트 전체에 적용되는 기본 인증 방식 정의
    - DEFAULT_AUTHENTICATION_CLASSES를 사용
        - 기본값: SessionAuthentication과 BasicAuthentication
        - TokenAuthentication (클라이언트 - 서버 설정에 적합)
        - RemoteUserAuthentication도 있다

```python
# settings.py에 
REST_FRAMEWORK = {
    # Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
```

1. view 함수 별 설정

```python
# view 함수에서 인증 데코레이터 부착
from rest_framework.decorators import authentication_classes
from rest_framework.permissions import TokenAuthentication, BasicAuthentication

@api_view(['GET', 'POST'])
@authentication_classes([TokenAuthentication, BasicAuthentication])
def article_list(request):
```

### TokenAuthentication 적용 방법(전역 설정)

- 인증 과정
    1. 사용자 로그인
    2. 서버 - DB에서 사용자 확인
    3. 서버에서 토큰 발급 → 클라이언트에 토큰과 함께 응답
    4. 클라이언트에서 토큰과 함께 데이터 요청 → 서버에서 토큰 검증
    5. 데이터 응답
- Django Signals
    - 이벤트 알림 시스템
    - 앱 내에 특정 이벤트 발생 → 다른 부분에 신호 보내서 이벤트 발생을 알림
    - 모델의 데이터 변경, 저장, 삭제 같은 작업에 반응 → 추가적인 로직 실행

```python
# settings.py
# 인증 클래스 설정
REST_FRAMEWORK = {
    # Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],

# INSTALLED_APPS 추가
INSTALLED_APPS = [
    'rest_framework.authtoken',
    ...

# migrate 후 accounts/signals.py 추가 (자동으로 뭔가 해주는 것들이 모여 있는 파일)
# 인증된 사용자에게 자동으로 토큰 생성해주는 역할
from django.db.models.signals import post_save
from django.dispatch import receiver
from rest_framework.authtoken.models import Token
from django.conf import settings

@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:
        Token.objects.create(user=instance)
```

### Dj-Rest-Auth 적용

- 회원가입, 인증(소셜미디어 인증 포함), 비밀번호 재설정, 사용자 세부 정보 검색, 회원 정보 수정 등 다양한 인증 관련 기능을 제공하는 라이브러리

```python
# settings.py
pip install dj-rest-auth==7.0.0            # 구버전은 오류 남
pip install dj-rest-auth[with-social]      # 등록 기능(소셜 로그인 포함 옵션)(이거 쓰자)

INSTALLED_APPS = [
		'rest_framework',

    'rest_framework.authtoken',

    'dj_rest_auth',                # 로그인 기능
    'django.contrib.sites',        # 등록 기능
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'dj_rest_auth.registration',
    ...

SITE_ID = 1     # 사이트 구분을 위해 아이디 부여

MIDDLEWARE = [
		'allauth.account.middleware.AccountMiddleware',
		...

# 프로젝트 파일에서 url 설정
path('accounts/', include('dj_rest_auth.urls')),
path('accounts/signup/', include('dj_rest_auth.registration.urls')),

# migrate 하기
```

### 토큰 사용법

1. 로그인 성공하면 토큰을 발급 받음
2. 인증이 필요한 요청을 할 때 header에 토큰을 함께 보냄
    - 형식을 맞춰서 보내기
    - axios 요청 시에 headers에 토큰을 담아서 요청

```python
headers: {
	Authorization: `Token ${토큰}`
```

### 인증 상태 여부에 따른 접근 제한

1. 인증되지 않은 사용자 제한
    - 토큰 소유 여부에 따라 로그인 상태를 저장
    - computed를 활용해 토큰 값이 변할 때만 상태 계산
2. 인증된 사용자는 회원가입, 로그인 페이지 접근 제한

```jsx
// counter.js
const isLogin = computed(() => {
		if (token.value === null) {
				return false
		} else {
				return true
		}
	
// router/index.js에 네비게이션 가드 활용
import { useCounterStore } from '@/stores/counter'

...

router.beforeEach((to, from) => {
  const store = useCounterStore()
  if (to.name === 'ArticleView' && !store.isLogin) {
    window.alert('로그인이 필요합니다.')
    return { name: 'LoginView' }
  }
  if ((to.name === 'SignUpView' || to.name === 'LogInView') && (store.isLogin)) {
    window.alert('이미 로그인이 되어 있습니다')
    return { name: 'ArticleView' }
  }
})
```

## 3. 권한

### 권한 적용 방법 2가지

1. 전역 설정
    - DEFAULT_AUTHENTICATION_CLASSES 사용
    - 기본값: rest_framework.permissions.AllowAny
    - IsAuthenicated, IsAdminUser, IsAuthenticatedOrReadOnly도 있음

```python
REST_FRAMEWORK = {
    # Authentication
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
    # permission
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ],
}
```

1. view 함수 별 설정

```python
# view 함수에서 권한 데코레이터 부착
from rest_framework.decorators import permission_classes
from rest_framework.permissions import IsAuthenticated

@api_view(['GET', 'POST'])
@permission_classes([IsAuthenticated])
def article_list(request):
```

### 회원가입 로직

```jsx
// View 페이지에서 store의 함수 호출, 이벤트 핸들러 달아주기
<form @submit.prevent="signUp">

-------------------------------------------------------------------------------------
const store = useCounterStore()

// 양방향 바인딩
const username = ref(null)
const password1 = ref(null)
const password2 = ref(null)

// django에서 지정한 이름들(username, email, password1, password2 잘 지키기)
const signUp = function () {
  const payload = {
    username: username.value,
    password1: password1.value,
    password2: password2.value,
  }
  store.signUp(payload)
}

------------------------------------------------------------------------------------
// counter.js에서 로직 구현
// payload는 백엔드에서 데이터 집합을 보낼 때 주로 사용하는 변수명
const signUp = function (payload) {
    // const username = payload.username
    // const password1 = payload.password1
    // const password2 = payload.password2

    const { username, password1, password2 } = payload

    axios({
      method: 'post',
      url: `${API_URL}/accounts/signup/`,
      data: {
        username, password1, password2,
      }
    })
      .then((response) => {
        console.log(response)
        console.log('회원가입 성공')
      })
      .catch((error) => {
        console.log(error)
      })
  }
```

### 로그인 로직

- 회원가입과 거의 동일, 서버로부터 응답 받은 토큰 저장 부분만 다름

```jsx
// counter.js
const token = ref(null)

const logIn = function (payload) {
    const { username, password } = payload

    axios({
      method: 'post',                       // 서버에서 토큰을 생성하므로 post 요청
      url: `${API_URL}/accounts/login/`,
      data: { username, password }
    })
      .then((response) => {
        console.log('로그인 성공');
        token.value = response.data.key     // 토큰 내용 저장
      })
  }
```

## 3. 환경 변수

- 개발, 테스트 및 프로덕션 환경에서 다르게 설정되어야 하는 설정 값이나 민감한 정보
- 환경 변수로 앱 설정 관리 → 다양한 환경에서 일관된 동작 유지
- 보안적인 이슈 피하고 앱을 다양한 환경에 대응하기 쉽게 만들어줌
1. .env.local 파일 생성 및 API 변수 작성
    - 변수명은 반드시 ‘VITE_’ 접두어 사용
    - 변수명과 값 사이에 공백 없애기

```jsx
const API_KEY = import.meta.env.VITE_TMDB_API_KEY
```