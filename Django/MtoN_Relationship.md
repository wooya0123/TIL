# M:N 관계
## 1. ManyToManyField()

- django에서 자동으로 중개모델을 생성
    - 중개모델: 여러 개의 외래키 필드를 가지고 둘의 관계를 관리하는 테이블
    - 참조하는 모델 어느 곳에 작성해도 상관없음(대등한 관계이기 때문)
        - 두 모델에 변화는 없음(참조/역참조 방향이 바뀔 수 있음)
        - 1:N 과 구분 짓기 위해 클래스 변수를 복수형으로 작성(ex. doctors)

```python
class Doctor(models.Model):
    name = models.TextField()

    def __str__(self):
        return f'{self.pk}번 의사 {self.name}'

class Patient(models.Model):
    # # Patient에서 Dortor쪽을 참조하는 ManyToManyField 작성(Reservation 모델을 통해서)
    doctors = models.ManyToManyField(Doctor, through='Reservation')
    name = models.TextField()

    def __str__(self):
        return f'{self.pk}번 환자 {self.name}'
        
class Reservation(models.Model):
    doctor = models.ForeignKey(Doctor, on_delete=models.CASCADE)
    patient = models.ForeignKey(Patient, on_delete=models.CASCADE)
    symptom = models.TextField()
    reserved_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.doctor.pk}번 의사의 {self.patient.pk}번 환자'
--------------------------------------------------------------------------------------
# shell_plus에서 사용할 때
patient1.doctors.add(doctor1)    # Patient 클래스의 클래스 변수 doctors를 활용해 중개 모델에 작성
doctor1.patient_set.add(patient2)    # Doctor 클래스 입장에서 Patient를 역참조해서 중개 모델에 작성

doctor1.patient_set.remove(patient1)    # Doctor 입장에서 Patient를 역참조해서 삭제
patient2.doctors.remove(doctor1)    # Patient 입장에서 Doctor를 참조해서 삭제

# 중개 모델에 id 외에 추가적인 필드에 내용 작성할 때
patient2.doctors.add(doctor1, through_defaults={'symptom': 'flu'})   # 인자로 딕셔너리 전달
```

### ManyToManyField 옵션

1. related_name
    - 역참조시 사용하는 manager 이름을 변경
    
    ```python
    doctors = models.ManyToManyField(Doctor, related_name='patients')
    
    doctor.patient_set.all() -> doctor.patients.all()   # 역참조
    ```
    
2. symmetrical
    - 관계 설정 시 대칭 유무 설정 (ManyToManyField가 동일한 모델을 가리킬 때만 사용)
    - 기본값: True
        - source 모델 → target 모델 참조 ⇒ target 모델도 source 모델 자동 참조
    
    ```python
    doctors = models.ManyToManyField(Doctor, symmetrical=False)
    ```
    
3. through
    - 사용하고자 하는 중개모델을 지정 → 추가 데이터를 M:N 관계와 연결하려는 경우

## 2. 좋아요 기능 구현

- N:1에서는 역참조 매니저 이름을 그대로 두기(user.article_set.all())
- M:N에서 N:1의 경우와 매니저 이름이 겹치면 이름을 바꿔주기 (related_name=’user.like_articles’)

```python
# 모델에 ManyToManyField 추가
class Article(models.Model):
    user = models.ForeignKey(
        settings.AUTH_USER_MODEL, on_delete=models.CASCADE
    )
    like_users = models.ManyToManyField(
        settings.AUTH_USER_MODEL, related_name='like_articles'
    )
    
# url 설정
path('<int:article_pk>/likes/', views.likes, name='likes'),

# view 설정
def likes(request, article_pk):
    article = Article.objects.get(pk=article_pk)
    if request.user != article.user:
		    if request.user in article.like_users.all():
		        article.like_users.remove(request.user)
		    else:
		        article.like_users.add(request.user)
    return redirect('articles:index')

# 템플릿 작성
{% if request.user != article.user %}    # 글의 작성자가 아니라면
  <form action="{% url "articles:likes" article.pk %}">
    {% csrf_token %}
    {% if request.user in article.like_users.all %}
      <input type="submit" value="좋아요 취소">
    {% else %}
      <input type="submit" value="좋아요">
    {% endif %}
  </form>
{% endif %}
```

## 3. 프로필 기능 구현

```python
# url 설정
# path('<str:username>/', views.profile, name='profile'),
# 이렇게 작성하면 이 아래의 모든 주소 문자열은 variable routing으로 인식 -> 맨 아래에 작성
path('profile/<str:username>/', views.profile, name='profile'),
-------------------------------------------------------------------------------------
# view 함수 설정
def profile(request, username):
    User = get_user_model()                         # 현재 페이지에 표시된 유저(활성화 유저)
    person = User.objects.get(username=username)    # 활성화된 유저 객체

    context = {
        'person': person,
    }
    return render(request, 'accounts/profile.html', context)
---------------------------------------------------------------------------------------- 
# 템플릿 생성
<h1>{{ person.username }}님의 프로필</h1>

<h2>{{ person.username }}가 작성한 게시글</h2>
{% for article in person.article_set.all %}
  <div>{{ article.title }}</div>
{% endfor %}

<h2>{{ person.username }}가 작성한 댓글</h2>
{% for comment in person.comment_set.all %}
  <div>{{ comment.content }}</div>
{% endfor %}

<hr>

<h2>{{ person.username }}가 좋아요한 게시글</h2>
{% for article in person.like_articles.all %}
  <div>{{ article.title }}</div>
{% endfor %}
```

## 4. 팔로우 기능 구현
```python
# model 설정
class User(AbstractUser):
    followings = models.ManyToManyField('self', symmetrical=False, related_name='followers')
---------------------------------------------------------------------------------------
# url 설정
path('<int:user_pk>/follow/', views.follow, name='follow'),
---------------------------------------------------------------------------------------
# view 함수 설정
def follow(request, user_pk):
    User = get_user_model()
    you = User.objects.get(pk=user_pk)  # 팔로우 요청을 보내는 대상
    me = request.user                   # 팔로우 요청하는 사람 (나 -> 너 팔로우 요청)

    if me in you.followers.all():
        you.followers.remove(me)
        # me.followings.remove(you)
    else:
        you.followers.add(me)
        # me.followings.add(you)
    
    return redirect('accounts:profile', you.username)
---------------------------------------------------------------------------------------
# 템플릿 수정
<div>
  팔로잉:{{ person.followers.all.count }} | 팔로워:{{ person.followings.all.count }}
</div>

{% if request.user != person %}
  <div>
    <form action="{% url "accounts:follow" person.pk %}" method="POST">
      {% csrf_token %}
      {% if request.user in person.followers.all %}
        <input type="submit" value="언팔로우">
      {% else %}
        <input type="submit" value="팔로우">
      {% endif %}
    </form>
  </div>
{% endif %}
```