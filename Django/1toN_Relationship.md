# 1:N 관계
- 외래키 클래스의 인스턴스 이름은 참조하는 모델 클래스 이름의 단수형으로 작성 권장
    - 테이블의 필드에 ‘_id’가 붙어서 생성
- 외래키는 클래스를 작성하는 위치와 상관없이 테이블의 마지막 필드로 생성됨
    - models.ForeignKey(참조하는 클래스 이름, on_delete=’참조하는 객체가 사라졌을 때 어떻게 할지’) - 옵션이 여러 개 있음

```python
from django.db.models import CASCADE

class Comment(models.Model):
    article = models.ForeignKey(Article, on_delete=models.CASCADE)
    content = models.CharField(max_length=200)
-----------------------------------------------------------------------    
# shell_plus를 사용할 경우
article = Article.objects.get(pk=1)
comment.article = article     # 외래키도 사용자가 직접 지정해줘야 함(자동으로 pk 추출)
```

- related manager를 통해 QuerySet API를 사용할 수 있음(ex. objects)
    - 모델명은 소문자로

<aside>

### CREATE

</aside>

```python
# form 생성
class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ('content',)    # 보여지는 form 화면에서는 내용만 보여주면 됨
-----------------------------------------------------------------------------------
# url 설정
path('<int:pk>/comments/', views.comments_create, name='comments_create'),

# view 함수(댓글은 GET method로 들어오는 경우 없음)
def comments_create(request, pk):
    article = Article.objects.get(pk=pk)
    comment_form = CommentForm(request.POST)
    if comment_form.is_valid():
		    # comment 인스턴스에 외래키를 저장하기 위해 DB에 저장하지 않고 인스턴스만 저장
        comment = comment_form.save(commit=False)
        comment.article = article     # 외래키 설정(외래키가 여러 개면 NULL 없이 다 설정)
        comment.save()
        return redirect('articles:detail', article.pk)
    context = {
        'article': article,
        'comment_form': comment_form,
    }
    return render(request, 'articles/detail.html', context)
```

<aside>


### READ

</aside>

```python
# detail 페이지에서 조회
def detail(request, pk):
    article = Article.objects.get(pk=pk)    # 보여줄 객체 조회
    comment_form = CommentForm()
    comments = article.comment_set.all()    # 보여준 객체를 기준으로 참조/역참조로 다수 조회

    context = {
        'comment_form': comment_form,
        'article': article,
        'comments': comments,
    }
    return render(request, 'articles/detail.html', context)
```

<aside>


### DELETE

</aside>

```python
# url의 통일성을 주기 위해 variable routing을 2개 사용
path('<int:article_pk>/comments/<int:comment_pk>/delete/', views.comments_delete, name='comments_delete'),
------------------------------------------------------------------------------------
def comments_delete(request, article_pk, comment_pk):
    comment = Comment.objects.get(pk=comment_pk)
    article = Article.objects.get(pk=article_pk)    # redirect를 위해 article도 조회
    comment.delete()
    return redirect('articles:detail', article.pk)
```

### admin site 등록

```python
# admin은 하나씩 등록해야함
admin.site.register(Article)
admin.site.register(Comment)
```

### 댓글이 없는 경우 DTL

```python
{% for comment in comments %}
    <li>
      {{ comment.content }}
      <form action="{% url "articles:comments_delete" article.pk comment.pk %}" method="POST">
        {% csrf_token %}
        <input type="submit" value="삭제">
      </form>
    </li>
{% empty %}
	<p>댓글이 없어요...<p>    # 데이터가 없는 경우 출력될 문구
{% endfor %}
```

### 댓글 개수 출력

```python
# DTL filter - length 사용
<p>{{ comments|length }}개의 댓글</p>
<p>{{ article.comments_set.all|length }}</p>

# QuerySet API 사용
<p>{{ article.comments_set.count }}</p>
```

## 2. 유저 - 댓글 연결

```python
from django.conf import settings

# 기존 모델에 필드 추가
class Article(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
```


<aside>


### CREATE

</aside>

```python
# 게시글 생성 시 바로 DB에 저장하지 않고 유저 id를 외래키로 저장 후 save
def create(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST)
        if form.is_valid():
            article = form.save(commit=False)
            article.user = request.user
            article.save()
            return redirect('articles:detail', article.pk)
    else:
        form = ArticleForm()
    context = {
        'form': form,
    }
    return render(request, 'articles/create.html', context)
```

<aside>

### UPDATE

</aside>

```python
# 조회한 글이 본인의 글인 경우만 수정할 수 있게
def update(request, pk):
    article = Article.objects.get(pk=pk)
    if request.user == article.user:
        if request.method == 'POST':
            form = ArticleForm(request.POST, instance=article)
            if form.is_valid():
                form.save()
                return redirect('articles:detail', article.pk)
        else:
            form = ArticleForm(instance=article)
    else:
        return redirect('articles:index')

    context = {
        'article': article,
        'form': form,
    }
    return render(request, 'articles/update.html', context)
```

```html
# 본인의 글일 때만 수정/삭제 버튼이 보이도록
{% if request.user == article.user %}
  <a href="{% url "articles:update" article.pk %}">수정</a><br>
  <form action="{% url "articles:delete" article.pk %}" method="POST">
    {% csrf_token %}
    <input type="submit" value="삭제">
  </form>
{% endif %}
```

<aside>

### DELETE

</aside>

```python
# 본인의 글일 때만 삭제할 수 있도록
def delete(request, pk):
    article = Article.objects.get(pk=pk)
    if request.user == article.user:
        article.delete()
    return redirect('articles:index')
```

## 3. Allowed HTTP methods

- 특정 HTTP method로만 View 함수에 접근할 수 있도록 제한하는 데코레이터
- 지정되지 않은 HTTP method로 요청이 들어오면 405 오류 반환
1. require_http_methods(["method1"...])
    - 지정된 HTTP method만 허용
2. require_safe()
    - GET과 HEAD method만 허용
    - 이 둘은 안전한 method로 간주 → 웹 표준을 준수하고 더 넓은 범위의 클라이언트와 호환
3. require_POST()
    - POST method만 허용

```python
from django.views.decorators.http import require_http_methods

@require_http_methods(['GET', 'POST'])
def func(request):
	pass
	
@require_safe
def func(request):
	pass
```

## 4. ERD

- 데이터베이스의 구조를 시각적으로 표현하는 도구
    - 시각적 모델링으로 효과적인 의사소통 지원
    - 실제 시스템 개발 전 데이터 구조 최적화에 중요
- Entity, 속성, 둘 간의 관계를 그래픽 형태로 시스템의 논리적 구조를 모델링하는 다이어그램
- 구성요소
    - Entity
        - 데이터베이스에 저장되는 객체나 개념
        - ex. 고객, 주문, 제품
    - 속성
        - 엔티티의 특성이나 성질
        - ex. 고객(이름, 주소, 전화번호)
    - 관계
        - 엔티티 간의 연관성
        - ex. 고객이 ‘주문’한 제품
- Cardinality
    - 엔티티와 엔티티 간의 수적 관계를 나타내는 표현 (1:1, N:1, M:N)