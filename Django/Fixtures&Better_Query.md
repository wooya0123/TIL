# Django 추가 정보
## 1. Fixtures

- Django가 DB로 가져오는 방법을 알고 있는 데이터 모음
- 앱에 초기 데이터를 제공 (직접 만들지 말 것)
- dumpdata
    - 모든 데이터 추출

```python
# ~~ 앱이름.모델명 > json 파일명
python manage.py dumpdata --indent 4 accounts.user > users.json
python manage.py dumpdata --indent 4 articles.article > articles.json
```

- loaddata
    - Fixtures 데이터를 DB로 불러오기’
    - 설치된 모든 app의 디렉토리에서 fixtures 폴더 이후의 경로로 fixtures파일을 찾아 load
    - 한 번에 실행 가능
        - 별도로 실행한다면 서로 연결된 관계에 따라 load 순서가 중요할 수 있음

```python
python manage.py loaddata users.json articles.json
```

- loaddata시 encoding codec 관련 에러 발생 시
    1. python -Xutf8 [manage.py](http://manage.py) dumpdata ~~
    2. 메모장으로 json 파일 열기 → 다른 이름으로 저장 → 인코딩을 UTF8 선택

## 2. Query 개선

- DB 측에 보내는 query 개수를 줄여서 조회

### annotate

- SQL의 GROUP BY를 사용, 집계 함수(count, sum 등)와 함께 자주 사용됨
1. 이렇게 조회할 경우 매번 count를 요청 → article과 comment를 묶어서 요청

```python
articles = Article.objects.order_by('-pk')
{{ article.comment_set.count }}    # 받아온 article에는 comment 정보가 없어 역참조 반복

-> articles = Article.objects.annotate(Count('comment')).order_by('-pk')
{{ article.comment__count }}   # 언더바 2개 사용
```

### select_related

- SQL의 INNER JOIN을 사용
- 1:1 N:1 참조 관계에서 사용

```python
articles = Article.objects.select_related('user').order_by('-pk')   # user까지 한 번에
{{ article.user.username }}    # 저장된 데이터에서 조회
```

### prefetch_related

- Python을 사용한 JOIN (관련 객체들을 미리 가져와 메모리에 저장해둠)
- M:N 또는 N:1 역참조 관계에서 사용

```python
articles = Article.objects.prefetch_related('comment_set').order_by('-pk')

{% for comment in article.comment_set.all %}   # 추가적인 데이터베이스 query없이 실행
  <p>{{ comment.content }}</p>
{% endfor %}
```

### select_related & prefetch_related

```python
articles = Article.objects.prefetch_related(
    Prefetch('comment_set', queryset=Comment.objects.select_related('user'))
).order_by('-pk')
```

### .exists()

- QuerySet에 결과가 하나 이상 존재하는지 여부를 확인
- 전제 QuerySet 평가 X, 결과의 존재 여부만 확인

```python
if request.user in article.like_users.all():
--> if article.like_users.filter(pk=request.user.pk).exists():
```