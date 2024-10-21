### Serialization(직렬화)

- 여러 시스템에서 활용하기 위해 데이터 구조나 객체 상태를 나중에 재구성할 수 있는 포맷으로 변환

### Serializer 클래스, ModelSerializer 클래스

- Serialization 진행

```python
# serializers.py 파일 생성해서 작성
from rest_framework import serializers

# 1. 필요에 따라 serializer를 여러 개 만들어서 사용
class ArticleListSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ('id', 'title', 'content')
        
-----------------------------------------------------------------------------------
# 2. 만약 외래키 필드를 포함하고 있는 모델을 참조한다면 해당 필드를 읽기 전용으로 설정
class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = '__all__'
        read_only_fields = ('article',)    # 유효성 검사 제외, 데이터 조회 시에는 출력

------------------------------------------------------------------------------------
# 3. 만약 serializer에서 출력되는 부분을 커스텀할 때
class CommentSerializer(serializers.ModelSerializer):
    # 이 클래스를 바깥에 작성해도 되지만 재사용 계획이 없다면 안에 작성해도 됨
    class ArticleTitleSerializer(serializers.ModelSerializer):
        class Meta:
            model = Article
            fields = ('title',)
    
    # 커스텀하면 메타 데이터에 read_only가 작동하지 않기 때문에 serializer 옵션 사용
    # 커스텀한 필드는 출력되는 클래스의 필드에 포함되어야 함
    article = ArticleTitleSerializer(read_only=True)

    class Meta:
        model = Comment
        fields = '__all__'    # 여기에 포함되어야 함

-------------------------------------------------------------------------------------
# 4. 만약 역참조 데이터를 같이 출력하고 싶다면 1의 클래스 안에 N의 클래스를 작성
class ArticleSerializer(serializers.ModelSerializer):
    class CommentDetailSerializer(serializers.ModelSerializer):
        class Meta:
            model = Comment
            fields = ('id', 'content',)

		# 클래스 변수(=역참조 매니저명)를 생성 -> 위에서 생성한 클래스의 데이터를 담기
		# 사용자가 작성하는 데이터가 아니므로 read_only 사용, QuerySet이니까 many 작성
		# 역참조 manager 이름도 바꿀 수 있음(model에서 related_name 작성 -> 클래수 변수 이름 변경)
    # 커스텀한 필드는 출력되는 클래스의 필드에 포함되어야 함
    comment_set = CommentDetailSerializer(read_only=True, many=True)

		# 조작한 데이터를 출력하려면 클래스 변수 생성 -> 아래 문법처럼 작성
		# source 뒤에 역참조할 때 article은 ArticleSerializer 내부라서 생략
		number_of_comments = serializers.IntegerField(source='comment_set.count', read_only=True)

    class Meta:
        model = Article
        fields = '__all__'    # 여기에 포함되어야 함

-------------------------------------------------------------------------------------

# 프로젝트 url 설정(
path('api/v1/', include('articles.urls')),    # api 엔드포인트/버전/리소스 - REST API 원칙

# url 설정(이제 template은 프론트엔드 프레임워크가 할 것이므로 이름 설정 X)
path('articles/', views.article_list),
path('articles/<int:article_pk>/', views.article_detail),
path('comments/', views.comment_list),
path('comments/<int:comment_pk>/', views.comment_detail),
path('articles/<int:article_pk>/comments/', views.comment_create),
```

### View 함수 작성

```python
from .serializers import ArticleListSerializer, ArticleSerializer
from rest_framework.response import Response      # 응답 상태 반환 클래스
from rest_framework.decorators import api_view    # DRF에서 꼭 써야하는 view 데코레이터
from rest_framework import status                 # 응답 상태를 정리해놓은 클래스

# 전체 데이터베이스 대상
@api_view(['GET', 'POST'])    # --> 이걸 안 쓰면 CSRF 토큰 오류가 발생
def article_list(request):
		# 데이터를 조회할 때
    if request.method == 'GET':
        articles = Article.objects.all()     # 전체 데이터
        serializer = ArticleListSerializer(articles, many=True)    # 쿼리셋을 변환
        return Response(serializer.data)
    
    # 데이터를 전송, 데이터베이스 수정
    elif request.method == 'POST':
        serializer = ArticleSerializer(data=request.data)    # 인자 꼭 쓰기
        # 유효성 검사
        if serializer.is_valid():
            serializer.save()
            # 저장 성공 후 201 응답 상태코드 반환('status='안 쓰면 200이 기본)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        # 저장 실패 후 400 응답 상태코드 반환
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
-----------------------------------------------------------------------------------

# 단일 데이터 대상
@api_view(['GET', 'DELETE', 'PUT'])
def article_detail(request, article_pk):
    article = Article.objects.get(pk=article_pk)     # 단일 데이터

		# 단일 데이터 조회
    if request.method == 'GET':
        serializer = ArticleSerializer(instance=article)   # instance가 첫 번째 인자라 생략 가능
        return Response(serializer.data)
    
    # 단일 데이터 삭제(사용자에게 데이터 보여줄 필요 X)
    elif request.method == 'DELETE':
        article.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
    
    # 단일 데이터 수정(데이터 넣고, partial도 넣는 것 추천)
    # partial: 부분 업데이트(모든 필드에 대해 수정사항 적지 않아도 오류 발생 X)
    elif request.method == 'PUT':
        serializer = ArticleSerializer(article, data=request.data, partial=True)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_200_OK)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
 -----------------------------------------------------------------------------------
 
# 외래키 필드를 가진 모델을 작성할 때
@api_view(['POST'])
def comment_create(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    serializer = CommentSerializer(data=request.data)
    if serializer.is_valid(raise_exception=True):
        serializer.save(article=article)    # 외래키 필드의 값을 채워줌
        return Response(serializer.data, status=status.HTTP_201_CREATED)

```

### 옵션: raise_exception=True

- DRF가 제공하는 기본 예외 처리기 → 예외 발생 시 HTTP 400 응답 반환

```python
if serializer.is_valid(raise_exception=True):
```

### 404 오류

- GET method의 특징
    1. 조회한 객체가 없을 때 DOESENOTEXIST 예외 발생
    2. 조회한 객체가 2개 이상일 때 MultiPle…
    - 서버는 예외 발생하면 코드 중단 → try-except 사용
- get_object_or_404()
    - 모델 manager objects에서 get()을 호출
    - 객체가 없으면 예외 대신 Http404 raise함
- get_list_or_404()
    - filter()를 호출
    - 객체 목록이 없으면 예외 대신 Http404 raise함
    - 데이터가 없어도 화면 측에서 사용자에게 알림을 줄 수 있다면 사용 가능
        - 프론트 측에서 404 에러를 처리해줬다면 사용

```python
from django.shortcuts import get_object_or_404

articles = Article.objects.all() --> get_object_or_404(Article)
article = Article.objects.get(pk=article_pk) --> get_object_or_404(Article, pk=article_pk)
```

### Serializer를 활용하는 이유

- ORM을 통해서 DB 조작 모두 가능 but, Serializer가 확장성이 좋음
- Serializer의 클래스명을 명시적으로 작성하면 재사용성도 좋음

### OpenAPI Specification(OAS)

- RESTful API를 설명하고 시각화하는 표준화된 방법
- API에 대한 세부사항을 기술할 수 있는 공식 표준
- drf-spectacular 공식 문서 참조(swagger, redoc 페이지 제공)