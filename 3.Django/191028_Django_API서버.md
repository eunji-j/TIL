# 191028_Django_REST framework

> api서버를 만들어보자 -> **json**형태의 데이터로만 사용자에게 응답 (**html  X**)
>
> 데이터를 관리하는 **서버(백엔드)**만 다룬다.

## 새로 배운 것

## 1)  django REST framework

> [공식사이트](https://www.django-rest-framework.org/)

### 1. 환경설정

프레임워크 설치

```python
pip install djangorestframework
```

api > `settings.py`

```python
'rest_framework',
```

### 2. 모델링

musics> `models.py` 아티스트, 음악, 댓글 모델생성

```python
from django.db import models

# Create your models here.
class Artist(models.Model):
    name = models.CharField(max_length=100)
    # 값을 확인하기 편리하다.
    def __str__(self):
        return self.name

class Music(models.Model):
    artist = models.ForeignKey(Artist, on_delete=models.CASCADE)
    title = models.TextField()
    def __str__(self):
        return self.title

class Comment(models.Model):
    music = models.ForeignKey(Music, on_delete=models.CASCADE)
    content = models.TextField()
    def __str__(self):
        return self.content
```

musics > `admin.py` 관리자 페이지에 모델 등록

```python
from django.contrib import admin
from .models import Artist, Music, Comment
# Register your models here.

admin.site.register(Artist)
admin.site.register(Music)
admin.site.register(Comment)
```

### 3. json 데이터 불러오기

> 데이터를 json형태로 변경해준다 -> `my_dumpdata.json` 파일 생성 (**복제**)

```
python manage.py dumpdata --indent 2 musics > my_dumpdata.json
```

> 데이터를 **불러와서 사용**한다. -> 모델링 다시 해줘야한다.

```
python manage.py loaddata musics/my_dumpdata.json
```

### 4.경로설정 (직렬화 작업)

api > `urls.py` 에서 api 서버 경로를 구성한다. (기존의 html방식과 다르다.) ★

```python
# api서버경로구성 -> 버전명시가 일반적(선택사항)
path('api/v1/', include('musics.urls')),
```

musics > `urls.py` 에서 **뮤직리스트와 뮤직상세** 2개의 경로를 설정한다.

```python
from django.urls import path
from . import views

app_name = 'musics'

urlpatterns = [
    path('musics/', views.music_list, name='music_list'), # html이 아니기 때문에 name은 적지 않아도 된다.
    path('musics/<int:id>/', views.music_detail, name='music_detail'),
]
```

musics > `serializers.py` 파일 생성하고 공식사이트 튜토리얼 1에서 코드 복붙한다. ★

> **직렬화해서 데이터 관리**한다. ex) 가수1-노래1-댓글1, 가수1-노래1-댓글2, 가수1-노래1-댓글3 ...

```python
from rest_framework import serializers
from .models import Music

class MusicSerializer(serializers.ModelSerializer):
    class Meta:
        model = Music
        fields = ('id', 'title', 'artist_id',)
```

musics > `views.py` 

```python
from django.shortcuts import render
from .models import Music
# 필요한 임포트
from rest_framework.decorators import api_view
from .serializers import MusicSerializer
from rest_framework.response import Response


# Restframework 요청, 응답
@api_view(['GET'])
def music_list(request):
    musics = Music.objects.all()
    # serializer: 직렬화작업 후 데이터리턴
    serializer = MusicSerializer(musics, many=True) # 데이터 여러개
    return Response(serializer.data)

@api_view(['GET'])
def music_detail(request, id):
    music = get_object_or_404(Music, id=id)
    serializer = MusicSerializer(music) # 데이터 한개
    return Response(serializer.data)
```



**postman google Chrome** > 확장프로그램 추가

> 요청을 보내면 api만 보내주는 것을 확인할 수 있다.

```python
GET -> 127.0.0.1:8000/api/v1/musics/  # 뮤직리스트
GET -> 127.0.0.1:8000/api/v1/musics/1  # 뮤직상세
```



## drf-yasg

[깃허브사이트](https://github.com/axnsan12/drf-yasg)

### 1.환경설정

```
pip install drf-yasg
```



```
'drf_yasg',
```





insta > `settings.py` 에서 **환경설정**

```python
INSTALLED_APPS = [
    # allauth 세팅(맨위)
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    ...
    # allauth 세팅
    'django.contrib.sites',
    # 필요한 제공자 추가
    'allauth.socialaccount.providers.kakao',
]

...

# allauth 세팅
AUTHENTICATION_BACKENDS = (
    # Needed to login by username in Django admin, regardless of `allauth`
    'django.contrib.auth.backends.ModelBackend',

    # `allauth` specific authentication methods, such as login by e-mail
    'allauth.account.auth_backends.AuthenticationBackend',
)

SITE_ID = 1
```

2. insta > `urls.py` 에서 **환경설정**

```python
# allauth 세팅(아래에 위치)
path('accounts/', include('allauth.urls')),
```

3. `python manage.py migrate`

4. 카카오개발자 > 로그인 > 앱 만들기 > 내애플리케이션설정

```html
-사용자관리 > 상태 ON > 로그인 Redirect URI에 다음 주소 추가
	http://127.0.0.1:8000/oauth
	https://127.0.0.1:8000/oauth
	http://127.0.0.1:8000/accounts/kakao/login/callback/
-고급 > Client Secret 상태 ON: 키값받기
```

5. admin페이지 > **Add social application** 생성

```html
-Provider: 설정
-Name: 설정
-Client id: 일반 > RestAPI Key 입력
-Secret key: 고급 > Client Secreat Key 입력
-Site: 아무거나 하나 추가
```

6. accounts > `form.html` 에서 **버튼과 경로 추가**

```html
<!-- 추가 -->
{% load socialaccount %}

<!-- 버튼과 경로 추가 -->
<a class="btn btn-warning" href="{% provider_login_url 'kakao'  %}">카카오로그인</a>
```

<br>

### 3) Django Bootstrap Pagination (페이지 넘기는 기능)

> [공식사이트](https://pypi.org/project/django-bootstrap-pagination/)

pagination 설치

```
pip install django-bootstrap-pagination
```

insta > settings.py

```
INSTALLED_APPS = (
	'bootstrap_pagination',
)
```

posts > views.py

```python
def index(request):
    posts = Post.objects.all()
    # 한페이지에 몇개를 보여줄지
    paginator = Paginator(posts, 5) 

    page = request.GET.get('page')
    posts = paginator.get_page(page)

    comment_form = CommentForm()
    context = {
        'posts': posts,
        'comment_form': comment_form,
    }
    return render(request, 'posts/index.html', context)
```

posts > index.html 에서 다음 코드 작성

```
{% load bootstrap_pagination %}
```

```
{% bootstrap_paginate page_obj %}
```

<br>

### 4) Heroku (배포)

> [공식사이트](https://www.heroku.com/)

#### 1. 서버 설정

1. insta > settings.py

```python
import django_heroku
django_heroku.settings(locals())
```

2. heroku 설치

```
pip install django-heroku
```

3. 최상위 > **Procfile** 파일 생성 후

```
web: gunicorn insta.wsgi --log-file -
```

4. gunicorn 설치

```
pip install gunicorn
```

5. 최상위 > **rumtime.txt** 파일 생성

```
python-3.7.4
```

6. 어떤 버전을 사용하는지 명시해주는 파일 생성

```
pip freeze > requirements.txt
```

#### 2. GitHub repository 생성 후 푸시

#### 3.Heroku 사이트

1. Deployment method > **GitHub 연결**
2. 생성한 **repository** 등록
3. `Automatic deploys` 버튼 클릭 > `Deploy Branch` 버튼 클릭하여 설치