# Django(4.2버전)
- 클라이언트: 서비스를 요청하는 주체
- 서버: 클라이언트의 요청에 응답하는 주체(웹 페이지, 앱을 저장하는 컴퓨터)

## 1. 가상 환경

- python 앱과 패키지들을 격리하여 관리할 수 있는 독립적인 환경
    - 전역 환경에서 개발하지 않고 프로젝트 별로 분리하여 개발
    - 가상 환경은 on / off의 개념 → 가상 환경을 켜야 함
        - on하면 모든 패키지가 설치되지 않은 초기 상태
    - 보통 venv를 프로젝트 파일에 같이 생성
    
    ```python
    python -m venv venv             # 가상 환경 생성
    source venv/Scripts/activate    # 가상 환경 경로를 통해 on -> (venv) 환경이라고 표시됨
    deactivate                      # 가상 환경 off (그냥 터미널을 꺼도 됨)
    pip list                        # 패키지 설치 현황 확인
    ```
    
    - 가상 환경의 패키지 목록들을 저장 → 협업하는 개발자에게 보여주기 위함
        - venv 파일의 용량이 크기 때문에 공유할 수 없음
    
    ```python
    pip freeze > requirements.txt    # pip 설치된 항목들을 출력하여 requirements.txt에 저장
    pip install -r requirements.txt  # requirements.txt에 있는 패키지를 한 번에 설치
    ```
    
- 의존성 패키지
    - 한 패키지가 다른 패키지의 기능이나 코드를 사용하기 때문에 그 패키지가 존재해야 함
    - 사용하려는 패키지가 설치되지 않았거나 호환되는 버전이 아니면 오류 발생할 수 있음
- 주의 사항
    - 활성화된 가상 환경에서 정확한 패키지 목록 생성
    - 시스템 전역 패키지와 구분 필요
    - 현재 터미널 환경에만 영향
    - 가상환경은 방이 아니라 ‘도구 세트’
    - venv는 프로젝트와 동일한 경로에 위치
    - venv는 gitignore에 작성되어 원격 저장소에 공유 X
        - requirements.txt를 공유

## 2. 프로젝트, 앱 생성

### 프로젝트 생성

- 현재 파일에 ‘firstpjt’라는 폴더(프로젝트)를 생성하고 manage.py를 생성
    - 이후로는 manage.py로 관리
    - 프로젝트명을 config로 하는 것도 괜찮음

```python
# .을 써야 현재 위치에 생성하고 파일 경로가 하나 더 안 생김
django-admin startproject firstpjt .
```

### 서버 실행

```python
python manage.py runserver
```

## 디자인 패턴

- 소프트웨어 설계에서 발생하는 문제를 해결하기 위한 일반적인 해결책
    - 애플리케이션 구조는 이렇게 구성하자 라는 관행
- MVC 디자인 패턴 (Model, View, Controller)
    - 애플리케이션을 구조화하는 대표적인 패턴
    - 데이터 / 사용자 인터페이스 / 비즈니스 로직을 분리 → 독립적이고 쉽게 유지 보수
- MTV 디자인 패턴 (Model, Template, View)
    - django에서 애플리케이션을 구조화하는 패턴
    - MVC 패턴과 동일하나 명칭을 다르게 정의한 것

### 프로젝트

- 1개의 프로젝트 안에 각 기능을 하는 앱들이 존재
    - 로그인 기능 앱, 게시판 앱, 댓글 앱 …
        
### 앱 생성

- 1 단계) 명령어 입력
    
    ```bash
    	python manage.py startapp 앱이름   # 앱은 . 안 붙여도 자동으로 현재 위치에 생성
    ```
    
    <aside>
    
    **앱 이름은 복수형으로 !**
    
     `posts` 앱의 주요 모델은 `Post`가 됨, 
    이렇게 하면 앱(**복수형**)과 모델(**단수형**) 사이의 관계가 명확해지기 때문에, 이러한 작명 규칙 또한 관행적
    
    </aside>
    
- 2 단계) 앱 등록
- `settings.py` 의 `INSTALLED_APPS` 리스트에 생성한 앱을 추가하자!!
    
    ```bash
    # settings.py
    INSTALLED_APPS = [
        '앱이름',    # 콤마 잊지 마세요.
        ....
    ]
    
    # 만약 base.html을 사용할 거면 settings.py에 base.html이 들어있는 경로 등록
    TEMPLATES = {
        'DIRS': [BASE_DIR / 'templates'],   # 최상위 폴더의 templates 폴더
        ...
    }
    ```
    
    <aside>
    ❗ **주의!!!!!!!!!!** 반드시 “생성 후 등록” 해야한다.
    settings.py에 미리 등록하고 생성하면 생성되지 않는다.
    
    </aside>
    

### 프로젝트 구성

- `settings.py`
    - **프로젝트 모든 설정을 관리**
- `urls.py`
    - **요청 들어오는 url에 따라 이에 해당하는 적절한 view를 연결**
- `__init__.py`
    - 해당 폴더를 패키지로 인식하도록 설정
- `asgi.py`
    - 비동기식 웹 서버와의 연결 관련 설정
- `wsgi.py`
    - 웹 서버와 연결 관련 설정(ex. AWS, Google)
- `manage.py`
    - django 프로젝트와 다양한 방법으로 상호작용 하는 커맨드라인 유틸리티

### 애플리케이션 구조

- `admin.py`
    - 관리자용 페이지 관련 코드를 작성하는 곳이다.
- `apps.py`
    - 애플리케이션의 정보가 있는 작성되어 있다.
    - 별도로 추가 코드를 작성하지 않는다.
- `models.py`
    - 애플리케이션에서 사용하는 Model을 정의하는 곳이다.
    - MTV 패턴의 M에 해당한다.
- `tests.py`
    - 프로젝트 테스트 코드를 작성하는 곳이다.
- `views.py`
    - view 함수들이 정의 되는 곳이다.
    - MTV 패턴의 V에 해당한다.

### 요청 & 응답

- URL → view → template

## 3. DTL(Django Template Language)

- 파이썬 코드와 비슷하지만 파이썬과 관련 없음
- 프로그래밍 로직은 view 함수에서 작성

### 1. 변수

- view 함수에서 context 딕셔너리를 생성 → render함수의 3번째 인자로 전달
    - template에서 변수명에 context 딕셔너리의 key값을 사용해 value값을 출력함
    - ‘.’을 사용하면 변수 속성에 접근 가능 (딕셔너리 안에 딕셔너리)

```python
{{ variable }}
{{ variable.attribute }}
```

### 2. 필터

- 표시할 변수를 수정할 때 사용
- chained(연결)이 가능하고 일부 필터는 인자를 받기도 함
- 60개 정도 built-in-template 필터가 있음

```python
{{ variable|filter }}
{{ name|truncatewords:30 }}
```

### 3. 태그

- html 파일에서 반복 또는 논리를 수행해 제어 흐름을 만듦
    - 일부 태그는 시작과 종료 태그 필요
- 24개 정도 있음

```python
{% tag %}
{% if foods|length == 0 %} {% else %} {% endif %}
{% for food in foods %} {% endfor %}
```

### 4. 주석

```python
{# name #}
{% comment %}
...
{% endcomment %}
```

### 템플릿 상속

- 페이지 공통 요소 포함
    - base.html에 skeleton 템플릿을 작성
    - 하위 템플릿에는 skeleton 템플릿을 제외한 내용만 작성하면 됨
    
    ```python
    # settings.py에 설정
    TEMPLATES = [
        {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],    # 최상위 폴더(BASE_DIR)로부터 경로 작성
    ```
    
- block 태그
    - 하위 템플릿에서 재정의 할 수 있는 블럭 정의
        - 상위 템플릿에 작성, 하위 템플릿이 작성할 수 있는 공간 지정
        - 각 block의 이름으로 block마다 하위 템플릿 작성 가능
            - 하위 템플릿에 block으로 감싸주고 내용 작성
    
    ```python
    {% block name %}
    {% endblock name %}
    ```
    
- extends 태그
    - 자식 템플릿이 부모 템플릿을 확장한다
    - 자식 템플릿 최상단에 작성되어야 함 (2개 이상 사용 불가)
    - 상위 템플릿의 경로를 작성해줘야 함
    
    ```python
    {% extends "articles/base.html" %}
    ```
    

### 요청 & 응답

- form 태그 - 서버에 요청을 보내는 가장 편리하고 안전한 방법 (웹 표준)
- url의 형태는 ‘?’를 기준으로 2개로 나눠짐
    - 입력 데이터가 전송될 URL ? Query String Parameters (&로 연결된 key-value 쌍)
- action
    - 입력 데이터가 전송될 URL 지정
    - 지정하지 않으면 현재 form이 있는 페이지의 URL로
- method
    - 데이터를 어떤 방식으로 보낼 것인지
    - method는 GET을 사용하고 로그인 시에 POST를 사용할 예정
- input에 값을 받아서 action에 작성된 주소로 요청을 보냄
    - name을 작성해줘야 함 → 입력 데이터에 붙이는 이름(key)
        - name을 key값으로 서버는 사용자가 입력한 데이터(value)에 접근

```python
# 검색하는 템플릿
{% extends "articles/base.html" %}

{% block content %}
  <h1>Fake naver</h1>
  <form action="https://search.naver.com/search.naver" method="GET">
    <input type="text" name="query">    # 사용자가 입력한 값에 key를 붙임
    <input type="submit">
  </form>
{% endblock content %}
```

- HTTP request 객체 활용
    - django로 들어오는 모든 요청 관련 데이터가 class형태로 저장됨 (view 함수의 첫 번째 인자)
    - form에서 사용자가 GET method로 보낸 데이터는 request 객체의 GET 딕셔너리에 담겨 있음

```python
def catch(request):
    # 사용자가 요청 보낸 데이터를 추출해서 context 딕셔너리에 세팅
    message = request.GET.get('message')
    context = {
        'message': message,
    }
    return render(request, 'articles/catch.html', context)
```

- throw - catch
    - throw에서 입력한 데이터를 catch로 보내서 catch 함수에 담긴 context에 값을 응답

```python
{% extends "articles/base.html" %}

{% block content %}
  <h1>Throw</h1>
  <form action="http://127.0.0.1:8000/catch/" method="GET">
    <input type="text" name="message">
    <input type="submit">
  </form>
{% endblock content %}

----------------------------------------------------------------------

{% extends "articles/base.html" %}

{% block content %}
  <h1>Catch</h1>
  <p>당신이 입력한 데이터는 {{ message }}입니다.</p>
{% endblock content %}
```
## 4. URL

- URL dispatcher
    - URL 패턴이 일치하는 요청을 view 함수로 연결(매핑)

### variable routing

- URL일부에 변수를 포함
- 변수를 view 함수의 인자로 전달 가능 (str, int 등 5가지 타입 가능)
    - 변수가 여러 개라면 앞에서부터 순서대로 view 함수에 작성해주면 됨

```python
# URL에 변수 포함(변수 타입:변수명)
path('hello/<str:name>', views.greeting)

# view 함수에 인자로 사용
def greeting(request, name):   # url에서 작성한 변수를 view 함수의 인자로 전달
    context = {
        'name': name,
    }
    return render(request, 'articles/greeting.html', context)

# 템플릿에서 출력 시 주소 값 변수 사용
{% block content %}
  <h1>Greeting</h1>
  <p>{{ name }}님 안녕하세요!</p>    # 주소값의 변수를 받아서 value값을 출력
{% endblock content %}
```

### **URL mapping**

- view 함수 이름이 같을 때 or 같은 패턴의 URL 주소 사용할 경우
→ app마다 URL을 관리
- 프로젝트의 URL에는 각 app의 URL로 연결하도록 설정

```python
# project의 URL 파일에 경로 설정
from django.contrib import admin
from django.urls import path, include
# URL 일치하는 부분까지 자르고 후속 부분은 include된 URL로 전달

# articles 주소로 들어오면 articles app의 url로 연결
urlpatterns = [
    path("admin/", admin.site.urls),
    path('articles/', include('articles.urls')),
]

-----------------------------------------------------------------------------------

# app의 URL 파일 생성
from django.urls import path
from . import views    # 명시적 상대 경로 (현재 있는 곳에서 views를 import 하겠다)

# 모든 주소가 articles/~~로 변경됨
# 각 앱에 app_name 속성 지정(앱이 여러 개일 때 key 추가 = 이름에 성을 붙이기)
app_name = 'articles'
urlpatterns = [
    path('index/', views.index, name='index'),   # path 함수 3번째 인자에 name 속성 작성
    path('hello/<str:name>', views.greeting, name='hello'),
]

-----------------------------------------------------------------------------------

# html 파일에 작성
{% block content %}
  <h1>Throw</h1>
  # URL mapping을 하면 URL 자리에 태그 사용(app_name:path_name)
  # variable routing 있으면 URL 이름 뒤에 작성 가능
  <form action={% url 'articles:index' arg1 arg2 %} method="GET">
    <input type="text" name="message">
    <input type="submit">
  </form>
{% endblock content %}
```

## 5. Django Model

- DB 테이블을 정의하고 데이터 조작할 수 있는 기능들을 제공
- models 모듈의 model이라는 부모 클래스를 상속 받음
    - 상속을 활용한 프레임워크 기능 제공 → 테이블 구조를 어떻게 설계할지만 고민
- 필드(열)
- model field
    - 데이터의 유형(field types)과 제약 조건(field options)을 정의
    - 주요 데이터 유형
        - 문자열 필드
            - CharField()
                - 제한된 길이의 문자열 저장(max_length는 필수 옵션)
            - TextField()
                - 길이 제한이 없는 대용량 텍스트 저장(무한대 X_)
        - 숫자 필드
            - IntegerField, FloatField
        - 날짜 / 시간 필드
            - DataField
            - TimeField
            - DateTimeField
                - auto_now_add 옵션: 데이터가 처음 생성될 때만 자동으로 현재 날짜, 시간 저장(작성일)
                - auto_now 옵션: 데이터가 저장될 때마다 자동으로 현재 날짜, 시간 저장
                (수정일)
        - 파일 관련 필드
            - FileField, ImageField
            
    - 주요 필드 옵션
        - null
            - 데이터베이스에서 null 값을 허용할 지 여부(기본값: False)
        - blank
            - form에서 빈 값을 허용할 지 여부(기본값: False)
        - default
            - 필드의 기본값을 설정
    - 제약 조건
        - 특정 규칙을 강제하기 위해 테이블의 열이나 행에 적용되는 제한사항

### Migrations

- model 클래스의 변경사항을 DB에 최종 반영하는 방법
    - migrate 할 때 직접 만든 설계도가 잘 반영됐는지 확인

```python
# table과 field가 추가되거나 변경될 때만 새로 migrate(테이블들의 순서 바꾸는 건 그냥 해도 됨)
# model class(설계도 초안) -> migration 파일(최종 설계도)
python manage.py makemigrations

# migration 파일(최종 설계도) -> db.sqlite3(DB에 반영)
python manage.py migrate

# 추가 명령어
python manage.py showmigrations    # 파일들이 migrate 됐는지 아닌지 여부를 확인
python manage.py sqlmigrate articles 0001    # 해당 migrations 파일이 SQL 언어로 어떻게 번역되어 전달되는지 확인
```

- 설계도를 업데이트할 때 이전 설계도를 기반으로 만드는 경우도 있음
    - 의존성 때문에 이전 설계도를 지우면 안 됨
- Git으로 업로드할 때 설계도만 업로드 (데이터베이스는 올리지 않음)
- 데이터베이스 초기화
    1. migration 파일 삭제
    2. db.sqlite3 파일 삭제
    - __init__.py 파일과 migrations 폴더는 지우지 않도록 주의

### Automatic admin interface

- 추가 설치 및 설정 없이 자동으로 제공하는 관리자 인터페이스
    - 데이터 확인 및 테스트 등을 진행하는 데 매우 유용
- 계정 생성 후 admin.py에 model 등록

```python
python manage.py createsuperuser   # 관리자 계정 생성

from django.contrib import admin
from .models import Article

admin.site.register(Article)      # admin 사이트에 등록
```

## 6. QuerySet

### ORM

- 객체 지향 프로그래밍 언어를 사용해 호환되지 않는 유형의 시스템 간 데이터 변환 기술
    - django와 데이터베이스 간 언어가 다르기 때문에 소통 불가 → ORM이 해석
- 쓰는 이유
    1. 데이터베이스 추상화
        - 데이터베이스 시스템에 종속 X, 일관된 방식으로 데이터 다루기
    2. 생산성 향상
        - SQL 쿼리를 작성하는 대신 python 코드로 데이터베이스 작업
    3. 객체 지향적 접근
        - 데이터베이스 테이블을 python 객체로 다루기 → OOP의 이점 활용

### Query

- 데이터베이스에 특정 데이터를 보여 달라는 요청
    - 쿼리문을 작성한다 = 원하는 데이터를 위해 데이터베이스에 요청 보낼 코드를 작성한다
- 파이썬 코드 → ORM → SQL → 데이터베이스
데이터베이스 → ORM(QuerySet 자료형으로 변환) → 사용자

### QuerySet

- 데이터베이스로부터 전달 받은 객체 목록(데이터 모음)
    - 순회 가능, 1개 이상의 데이터 불러와서 사용 가능
- Django ORM을 통해 만들어진 자료형
- 데이터베이스가 단일 객체 반환할 때는 ~~QuerySet~~ → 모델 class의 instance로 반환
💡
### Create

```python
pip install ipython
pip install django-extensions    # settings에 'django_extensions' 추가

# django 환경 안에서 python shell 실행 -> QuerySet API 구문이 Django 프로젝트에 영향
python manage.py shell_plus    # 이 환경에서도 for문 같은 python 문법 실행 가능
```

### QuerySet API

- ORM(python 모델 클래스와 인스턴스 활용)에서 데이터 검색, 필터링, 정렬, 그룹화 하는 도구
- API를 사용해 SQL이 아닌 Python 코드로 데이터 처리

### 데이터 객체 생성 방법 3가지

1. 인스턴스 변수 생성 후 값 하나씩 저장

```python
article = Article()            # class로부터 instance 생성
article.title = 'first'        # 인스턴스 변수(title)에 값 할당
article.content = 'django!'    # 인스턴스 변수(content)에 값 할당
# datatimeField, id 등은 할당 안 해도 자동 생성

article.save()   # 객체를 데이터베이스에 저장
article.pk       # id 값 확인 (django에서는 id보다 pk를 권장)
```

2. **인스턴스 변수 생성 시에 값 할당 (주로 사용)**

```python
article = Article(title='second', content='django!')
article.save()
```

3. create() 메서드 활용
    - 생성~저장까지 한 번에
    - 단, 유효성 검사를 할 수 없음

```python
Article.objects.create(title='third', content='django!')
```

### View로 생성

```python
# 데이터 객체 생성 요청 시 생성 페이지로 연결
def new(request):
    return render(request, 'articles/new.html')
---------------------------------------------------------------------------------------
# form에 입력 후 create url로 데이터 전송, POST method 사용
{% block content %}
  <h1>생성 페이지</h1>
  <form action="{% url 'articles:create' %}" method="POST">
    {% csrf_token %}
    <label for="name">식당 이름 : </label>            # 라벨 태그와 input태그의 id 일치
    <input type="text" id="name" name="name"> <br>
    <input type="submit" value="CREATE">
  </form>
{% endblock content %}
--------------------------------------------------------------------------------------
# create url이 호출되고 create view 함수가 실행
from django.shortcuts import render, redirect
from .models import Article

def create(request):
    restaurant = Article()                      # 모델에서 클래스 불러와서 인스턴스 생성
    restaurant.name = request.POST.get('name')  # 요청이 온 request에서 데이터 조회
    restaurant.save()                           # 데이터베이스에 저장
    
    # POST이므로 새로운 페이지를 랜더링하지 않고 원래 있던 페이지로 GET 요청을 보내서 표시
    return redirect('articles:detail', restaurant.pk)  # detail 페이지는 pk값도 전달
```
### Read


### QuerySet API Method

**<조회 method>**

- 주어진 매개변수와 일치하는 객체를 포함하는 QuerySet 반환
1. **Article.objects.all()** → 전체 데이터 조회
2. **Article.objects.filter(title=’first’)** → title이 first인 객체를 포함하는 QuerySet 반환
    - 결과가 없어도 일단 빈 QuerySet 반환해줌
    - 필터로 QuerySet에서 각 인스턴스의 변수를 조회하려면
    
    ```python
    article = Article.objects.filter(title = 'first')
    article.title      # 불가
    article[0].title   # 가능
    ```
    
3. **Article.objects.get(pk=1)** → 매개변수와 일치하는 객체 반환
    - 객체이기 때문에 인스턴스 변수 바로 조회 가능
    - 결과가 없으면 예외 발생
    - 결과가 1개 이상이면 예외 발생
        - pk와 같이 고유성을 보장하는 조회에 사용

**<Field lookups>**

- Query의 조건을 구성하는 방법
- django queryset api 공식 문서를 참고해서 사용

```python
Article.objects.filter(title__contains = 'first')   # 'first'를 포함하는 객체 조회
```

### Views로 조회

```python
from .models import Article

# 전체 데이터 객체를 조회해서 context로 페이지에 전달
def index(request):
		articles = Articles.objects.all()
		context = {
				'articles': articles,
		}
		return render(request, 'articles/index.html', context)
---------------------------------------------------------------------
# 페이지에서 데이터 객체의 속성을 DTL로 출력
# 이 때 전달된 데이터는 QuerySet 객체이므로 순회를 돌아서 풀어줘야 속성 method를 쓸 수 있음
{% for article in articles %}
	<p>{{ article.pk }}</p>
{% endfor %}
```

### GET vs POST

1. **method = “GET”**
- **조회 요청에만 사용 = “페이지를 줘!”**
- URL 길이 제한 → 대량 데이터 전송에 적합X
- 요청 URL이 브라우저 히스토리에 남음
- 캐싱
    - GET 요청의 응답을 로컬에 저장 가능
    - 동일한 URL로 다시 요청할 때 서버 접속하지 않고 저장된 결과 사용

2. **method = “POST”**
- **조회 외의 모든 부분에 사용**
    - POST 요청 후 새로운 페이지를 응답하는 것은 어색한 방법 → 기본 페이지로 보내야 함
    - 사용자를 보낸다 = 사용자가 GET 요청을 한 번 더 보내도록 응답하는 것(Redirect)
- HTTP Body를 통해 데이터 전송 → 많은 양의 데이터 전송 가능
- 브라우저 히스토리 남지 않음
- 캐싱 X(서버의 상태를 변경하는 작업을 수행하기 때문)
- CSRF 토큰을 넣어야 함(Django가 만든 페이지임을 검증)
    - form 태그 안에 `{% csrf_token %}`을 넣어주면 됨
- redirect()
    - view 함수에서 render가 아닌 redirect 함수 적용

```python
from django.shortcuts import render, redirect

...
return redirect(request, 'create.html', 
```

### Update

```python
article = Article.objects.get(pk=2)    # 조회를 해서 변수에 저장
article.content = 'ssafy'              # 조회한 객체의 인스턴스 변수의 값을 변경
article.save()
```

### View로 수정

```python
# edit url로 들어왔을 때 view 함수 실행
# variable routing으로 받아온 pk값과 동일한 데이터 객체 조회 -> context에 담아서 전달
def edit(request, pk):
    article = article.objects.get(pk=pk)
    context = {
        'article' : article,
    }
    return render(request, 'articles/edit.html', context)
-------------------------------------------------------------------------------------
# 페이지에서 수정 내용을 form으로 입력 받고 update url로 수정 데이터 전송
# 사용자의 편의를 위해 input 창에 이전 데이터 표시(value 값으로 전달)
<form action="{% url 'articles:update' article.pk %}" method="POST">
    {% csrf_token %}
    <label for="name">식당 이름 : </label>
    <input type="text" id="name" name="name" value="{{ article.name }}"> <br>
    <label for="address">식당 주소 : </label>
    <textarea name="address" id="address" >   # textarea는 value값이 없어서
      {{ article.address }}                   # 태그 안에 내용 작성
    </textarea> <br>
    <input type="submit" value="UPDATE">
</form>
-------------------------------------------------------------------------------------
# update url을 통해서 view에서 update 함수 실행
# pk에 맞는 데이터 조회 -> 데이터 수정 -> 저장 -> redirect
def update(request, pk):
    article = Article.objects.get(pk=pk)
    article.name = request.POST.get('name')
    article.address = request.POST.get('address')
    article.save()
    return redirect('articles:detail', article.pk)
```


### Delete

</aside>

- 지워진 id는 더 이상 사용하지 않음
    - 2, 3번이 지워졌다면 다음 생성 객체는 4번

```python
article = Article.objects.get(pk=2)   # 조회
article.delete()
```

### View로 삭제

```python
# delete url로 요청 보내기
<form action="{% url 'articles:delete' article.pk %}" method="POST">
  {% csrf_token %}
  <input type="submit" value="DELETE">
</form>
-----------------------------------------------------------------------------------
# view에서 delete 함수 실행 -> 해당 pk 데이터 삭제 -> redirect
def delete(request, pk):
    article = Article.objects.get(pk=pk)
    article.delete()
    return redirect('articles:index')
```

## 7. Django Form

- **HTML form vs Django form**
    - HTML form
        - 사용자로부터 데이터를 제출 받기 위해 활용한 방법
        - 비정상적, 악의적인 요청을 필터링 불가 → 유효성 검사 추가 필요
    - Django form
        - 사용자 입력 데이터를 수집하고 처리, 유효성 검사를 수행
        - **Form vs ModelForm**
            - Form: 사용자 입력 데이터를 DB에 저장하지 않을 때(ex. 검색, 로그인)
            - ModelForm: 사용자 입력 데이터를 DB에 저장해야 할 때(ex. 게시글 작성, 회원가입)
                - 모델과 연결된 form을 자동으로 생성
                - Meta class: ModelForm의 정보를 작성하는 곳
                    - (참고) Meta data = 데이터의 데이터

```python
# forms.py 파일 생성
from django import forms

# Form class 정의
class ArticleForm(forms.Form):
   title = forms.CharField(max_length=10)
   content = forms.CharField(widget=forms.Textarea)    # textarea가 없기 때문에 위젯 사용
   
# 모델과 연결된 form을 만들고 싶다면!!
class ArticleForm(**forms.ModelForm**):   # !!여기 자꾸 까먹으니 주의!!
		# 기존 모델 기반으로 필드 속성을 재정의하기(위젯 속성은 검색해서 이용하기)
		title = forms.CharField(
        label='제목',
        widget=forms.TextInput(
            attrs={                                      # 이 속성은 HTML의 속성임
                'class': 'my-title',
                'placeholder': '제목을 입력해주세요',
                'maxlength': 10,
            }
        )
    )

    # 메타 클래스를 생성 -> 모든 필드를 가져온다 or 특정 필드는 제외한다(exclude)
    # 웬만하면 이곳에는 기본 속성만 작성하기
    class Meta:
        model = Article
        fields = '__all__'
        exclude = ('title',)
----------------------------------------------------------------------------------------
# view 함수에서 input 받는 함수 변경
from .forms import ArticleForm

def new(request):
    form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/new.html', context)
---------------------------------------------------------------------------------------
# 페이지에서 form 인스턴스 출력
<form action="{% url "articles:create" %}" method="POST">
  {% csrf_token %}
  {{ form.as_p }}   # form rendering option(label, input 쌍을 특정 HTML 태그로 감싸는 옵션)
</form>
```

---

### Create

</aside>

```python
# 페이지에서 데이터를 create url로 전송
<form action="{% url "articles:create" %}" method="POST">
  {% csrf_token %}
  {{ form.as_p }}
  <input type="submit"> 
</form>
-----------------------------------------------------------------------------------
# create url에서 view에 있는 create 함수 실행
def create(request):
    # 과거 create의 로직, 요청 method가 POST일 때
    # 데이터 유효성 검사를 해서 유효하면 해당 데이터를 저장 -> redirect
    if request.method == 'POST':
        # ModelForm은 자동으로 request.POST의 데이터명을 인식
        form = ArticleForm(data=request.POST)   # 첫 번째 인자가 'data='이므로 생략 가능
        if form.is_valid():
            article = form.save()
            return redirect('articles:detail', article.pk)
    
    # 과거 new의 로직, 요청 method가 POST가 아닐 때(GET, PUT, DELETE 등등)
    # 생성 페이지 GET 요청이 들어오면 or 유효하지 않은 데이터 -> 빈 생성 페이지를 전달
    else:
        form = ArticleForm()
    # 만약 유효하지 않은 데이터였다면 에러 메세지를 포함하여 form 전달됨
    context = {
        'form': form,
    }
    return render(request, 'articles/create.html', context)
```

- save()
    - 데이터베이스 객체 만들고 저장 - ModelForm의 인스턴스 메서드
    - 키워드 인자 instance 여부를 통해 생성할지, 수정할 지 결정

### Update

</aside>

```python
# update url로 수정 요청
<form action="{% url "articles:update" article.pk %}" method="POST">
  {% csrf_token %}
  {{ form.as_p }}
  <input type="submit" value="수정">
</form>
------------------------------------------------------------------------------------
# update url을 통해 update 함수 실행
def update(request, pk):
    article = Article.objects.get(pk=pk)
		
		# POST 요청이면 유효성 검사를 해서 데이터 저장
    if request.method == 'POST':
		    # pk에 맞는 객체 기반으로 form변수에 인스턴스 생성 + 인스턴스 인자로 기존 Article 객체 전달
        form = ArticleForm(data=request.POST,instance=article)
        if form.is_valid():
            form.save()
            return redirect('articles:detail', article.pk)
    
    # 생성 페이지 GET 요청이 들어오면 or 유효하지 않은 데이터 -> 기존 Article 객체 기반으로 form 전달
    else:
        form = ArticleForm(instance=article)
    context = {
        'article': article,    # 원래 저장된 데이터를 사용하기 위해
        'form': form,
    }
    return render(request, 'articles/update.html', context)
```

## 8. Static Files

- 서버 측에서 변경되지 않고 고정적으로 제공되는 파일(이미지, JS, CSS 등)
    - 정적 파일을 제공하기 위한 경로(URL)가 있어야 함

### 기본 경로

- static_URL
    - 서버에 저장된 static 파일을 불러올 때 사용
    - 실제 파일이나 디렉토리 경로가 아니고 경로에 위치한 정적 파일을 참조하기 위한 URL
    - URL/static_URL/정적파일 경로
        - 127.0.0.1:8000/articles/static/articles/sample-1.png

```python
# settings.py에 등록
STATIC_URL = 'static/'
--------------------------------------------------------------------------------------
{% load static %}    # 해당 페이지에서만 적용됨

# 경로: static 내 중간 폴더/이미지 파일 이름
<img src="{% static "articles/sample-1.png" %}" alt="sample-image">
```

### 추가 경로

```python
# settings.py에 등록
STATICFILES_DIRS =[
    # python의 객체 지향 경로 시스템
    BASE_DIR / 'static',
]
--------------------------------------------------------------------------------------
{% load static %}    # 해당 페이지에서만 적용됨

# 경로: 최상위 폴더에 만든 'static' 파일 내 이미지 이름
<img src="{% static "sample-1.png" %}" alt="sample-image">
```

### Media Files

- 사용자가 웹에서 업로드하는 정적 파일(user-uploaded)
- ImageField()
    - 이미지 업로드에 사용하는 모델 필드
    - 이미지 객체가 DB에 저장 X → 이미지 파일의 경로 문자열이 저장됨
1. MEDIA_ROOT
- 미디어 파일들이 위치하는 디렉토리의 절대 경로
2. MEDIA_URL
- MEDIA_ROOT에서 제공되는 미디어 파일(사용자가 업로드한 파일)에 대한 주소를 생성

```python
# settings.py에 설정
MEDIA_ROOT = BASE_DIR / 'media'   # media 폴더에 미디어 저장
MEDIA_URL = 'media/'              # 미디어 불러올 때 URL주소가 media/로 시작
```

3. MEDIA_ROOT와 MEDIA_URL에 대한 URL 지정

```python
# project의 url.py에 작성
from django.conf import settings
from django.conf.urls.static import static

   path('articles/', include('articles.urls')),
] + static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT)
```

4. 라이브러리 설치 & model 생성

```python
# Pillow 라이브러리 설치
pip install Pillow

# models.py에 이미지 필드 생성(어디에 적든 항상 맨 뒤에 필드 생성)
# 이미지가 없을 때를 고려, 경로 설정(media 폴더 안에 images 폴더에 넣겠다)
class Article(models.Model):
		image = models.ImageField(blank=True, upload_to='images/')   # 파일명에 저장
		image = models.ImageField(blank=True, upload_to='%Y/%M/%d')  # 날짜별로 폴더 생성, 저장
```

5. 페이지 수정 & view 함수 수정

```python
# form 태그에 미디어 받을 수 있도록 enctype 추가
<form action="{% url "articles:create" %}" method="POST" enctype="multipart/form-data">
  {% csrf_token %}
  {{ form.as_p }}
  <input type="submit">
</form>
------------------------------------------------------------------------------------

# view 함수에서 미디어 받아오기
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES)   # FILES에 저장돼 있음(2번째 인자)
        if form.is_valid():
            article = form.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/create.html', context)
-------------------------------------------------------------------------------------

def update(request, pk):
    article = Article.objects.get(pk=pk)
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES, instance=article)
        if form.is_valid():
            form.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm(instance=article)
    context = {
        'article': article,
        'form': form,
    }
    return render(request, 'articles/update.html', context)
```

6. 이미지 제공하기
    - URL 속성을 통해 파일의 경로 값 추출
        - 업로드 파일 경로{{ article.image.url }}
        - 업로드 파일의 파일 이름 {{ article.image }}
    - 동일한 이름의 이미지 파일 올려도 django가 알아서 이름 변경해서 저장