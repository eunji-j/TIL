# 191023_Django_N:M(팔로우, 해시태그)

> Instagram 사이트 기능을 구현해보자 !

## 새로 배운 것

### 1)  팔로잉: 팔로워 (N:M 관계)

<br>

#### 1. User 모델과 User모델폼 수정

accounts > `models.py` 에 User와 n:m관계의 **User모델을 수정 (확장)**한다.

```python
from django.db import models
from django.contrib.auth.models import AbstractUser # 상속받을 유저모델 임포트
from django.conf import settings 

# Create your models here.
class User(AbstractUser):
    # followers와 followings로 상호접근이 가능하도록 n:m 관계를 설정한다.
    # 클래스 이름인 User를 직접사용할 수 없기 때문에 settings.AUTH_USER_MODEL을 불러온다.
    # 결국 User모델에는 followers와 followings 두개의 컬럼이 생성된다.
    followers = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name="followings")
```

accounts > `admin.py` **관리자페이지**에 User와 UserAdmin모델을 **함께 등록**시켜준다.

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

# Register your models here.
# User만 설정하게 되면 관리자페이지에서 기본제공되는 User모델이 변경되어 보여지기 때문에
# UserAdmin도 넣어줌으로써 기존 User형태로 보이게 해준다.
admin.site.register(User, UserAdmin)
```

accounts > `forms.py` 에서 **회원가입 폼을 수정한 User모델로 적용하여 생성**한다.

```python
from django import forms
from django.contrib.auth.forms import UserCreationForm
from .models import User

# 수정한 User모델을 적용하기 위해 기존 회원가입폼을 상속받는 모델폼을 생성한다.
# 이때 Meta클래스 또한 상속을 받아야한다.
class CustomUserCreationForm(UserCreationForm):
    class Meta(UserCreationForm.Meta):
        model = User
        # fields = UserCreationForm.Meta.fields + ('',)
        # 추가시켜줄 필드가 있을 경우에 이렇게 작성한다.
```

accounts > `views.py` 에서 회원가입 폼을 기존의 UserCreationForm에서 **새로 등록한 CustomUserCreationForm으로 바꿔준다.**

```python
from .forms import CustomUserCreationForm # 폼,모델 임포트
from .models import User

# Create your views here.
def signup(request):
    if request.method == "POST":
        form = CustomUserCreationForm(request.POST) # 수정한 모델폼으로 설정
        if form.is_valid():
            form.save()
            return redirect('accounts:login')
    else:
        form = CustomUserCreationForm()
    context = {
        'form': form
    }
    return render(request, 'accounts/form.html', context)
```

로그인폼인 AuthenticationForm은 ModelForm이 아닌 Form을 상속받기 때문에 변경해주지 않아도 된다.

<br>

#### 2. 유저페이지 추가

accounts > `urls.py` 에 **유저페이지 경로**를 지정해준다.

```python
path('<int:id>/', views.user_page, name="user_page"),
```

accounts > `views.py`에 **유저페이지로 가는 함수**를 만들어준다.

```python
def user_page(request, id):
    user_info = get_object_or_404(User, id=id)
    context = {
        'user_info': user_info
    }
    return render(request, 'accounts/user_page.html', context)
```

accounts > templates > accounts > `user_page.html`를 생성하여 **유저페이지**를 만들어준다.

```html
{% extends 'base.html' %}
{% block body %}
  <h1>{{user.username}}님의 페이지입니다.</h1>
{% endblock %}
```

<br>

#### 3. 팔로우 기능 추가

accounts > `urls.py` 에 **팔로우 경로**를 지정해준다.

```python
path('<int:id>/follow/', views.follow, name='follow'),
```

accounts > `views.py` 에 **팔로우 함수**를 만들어준다.

```python
def follow(request, id):
    you = get_object_or_404(User, id=id)
    me = request.user
    # 내가 나를 팔로우할 수 없도록 설정
    if you != me:
        if you in me.followings.all():
            me.followings.remove(you)
            # you.followers.remove(me) 위와 동일
        else:
            me.followings.add(you)
            # you.followers.add(me)
    return redirect('accounts:user_page', id)
```

accounts > `user_page.html` 유저페이지에 **팔로우 기능 추가**해준다.

```html
{% extends 'base.html' %}
{% block body %}
  <!-- 아래의 시스템user와 충돌을 피하기 위해 이름을 변경한다.(user -> user_info) -->
  <h1>{{user_info.username}}님의 페이지입니다.</h1>
  <h5>팔로워 : {{user_info.followers.all | length}}</h5>
  <h5>팔로잉 : {{user_info.followings.all | length}}</h5>
  
  <!-- 로그인한 사용자에게만 보여주고, 내가 나를 팔로우하지 못하게 한다.  -->
  {% if user.is_authenticated and user != user_info %}
    <a href="{% url 'accounts:follow' user_info.id %}">팔로우</a>
  {% endif %}
{% endblock %}
```

<br>

### 2)  포스트: 해시태그 (N:M 관계)

posts > `models.py` 에서 n:m 관계인 **해시태그와 포스트모델**을 만들어준다.

```python
from django.db import models
from django.conf import settings
from imagekit.processors import ResizeToFill # 이미지파일업로드시 필요
from imagekit.models import ProcessedImageField


# Create your models here.
# 포스트에서 해시태그모델을 사용하기 때문에 상위에 위치해야한다.
class HashTag(models.Model):
    content = models.CharField(max_length=100)

class Post(models.Model):
    content = models.TextField()
    # 유저와 포스트 1:n관계
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    # 이미지파일 저장경로와 크기지정
    image = ProcessedImageField(
                # null=True, # 필드값 유효성검사할 때 빈값도 가능
                # blank=True, # 모델폼 입력할 때 빈값도 가능
                processors=[ResizeToFill(300,300)], # 비율이 안깨지게(리스트형태)
                format='JPEG',
                options={'quality':90}, # 생략해도 상관없음
                upload_to='media' # 경로(media폴더안에 폴더생성해준경우 폴더이름으로 설정)
            )
    # 유저와 포스트 n:m관계(좋아요)
    like_users = models.ManyToManyField(settings.AUTH_USER_MODEL, related_name="like_posts")
    created_at = models.DateTimeField(auto_now_add=True)
    # 포스트와 해시태그 1:n관계
    hashtags = models.ManyToManyField(HashTag, related_name="taged_post")
```

insta > `settings.py` > INSTALLED_APPS 에 앱 추가한다.

```
'imagekit',
```

posts > `forms.py` 에서 **포스트 생성 모델폼** 생성한다.

```python
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ('content', 'image',)
```

posts > `views.py` 에서 **포스트 생성 함수**를 생성한다.

```python
from .forms import PostForm
from .models import HashTag, Post


def create(request):
    if request.method == 'POST':
        # 이미지파일은 request.FILES안에 들어있다.
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.user = request.user
            # post object (None) 상태
            post.save()
            # post object 생성된 상태 -> 해시태그 추가
            for word in post.content.split():
                if word.startswith('#'): # word[0] == '#'과 동일
                    # get_or_create() 해당객체가 있으면 가져오고 없으면 생성한다.
                    # hashtag, created = HashTag.objects.get_or_create(content=word) # (obj, True or False) 튜플 반환
                    hashtag = HashTag.objects.get_or_create(content=word)[0] # 0번째 인덱스인 obj만 가져오겠다.
                    post.hashtags.add(hashtag)
            return redirect('posts:index')
    else:
        form = PostForm()
    context = {
        'form': form
    }
    return render(request, 'posts/form.html', context)
```

posts > `form.html`

```html
{% extends 'base.html' %}
{% load bootstrap4 %}
{% block body %}
  <form action="" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    {% bootstrap_form form %}
    <input type="submit">
  </form>
{% endblock %}
```

posts > `index.html`

```html
{% extends 'base.html' %}
{% block body %}
  {% for post in posts %}
    {% include 'posts/_post.html' %}
  {% endfor %}
{% endblock %}
```

posts > `_post.html`

```html
<div class="card col-6">
    <img src="..." class="card-img-top" alt="...">
    <div class="card-body">
      <h5 class="card-title"></h5>
      <p class="card-text">{{post.content}}</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>
```

insta > `settings.py` > 맨 아래에 다음 2개의 **미디어 경로**를 지정해준다.

```python
# urls.py 역할
MEDIA_URL = '/media/'

# 내부적으로 서버가 파일을 찾는 경로
MEDIA_ROOT = os.path.join(BASE_DIR)
# MEDIA_ROOT = os.path.join(BASE_DIR, 'media') # media폴더안에 폴더생성한경우 'media'까지 추가
```

insta > `urls.py` 에서 **미디어 url**을 추가해준다.

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

posts > `views.py` 에서 특정 **해시태그의 포스트를 찾아주는 함수**를 생성한다.

```python
def hashtags(request, id):
    hashtag = get_object_or_404(HashTag, id=id)
    posts = hashtag.taged_post.all
    context = {
        'posts': posts
    }
    return render(request, 'posts/index.html', context)
```



해시태그를 클릭하면 해당 포스트들 찾기

posts > **templatetags** 폴더를 생성하고, 그 안에 `__init__.py`  (이 폴더가 모듈로써 동작하게 해주는 역할) 과 `make_link.py` (템플릿 등록 함수 생성하는 역할) 를 생성한다.

```python
from django import template

register = template.Library()

@register.filter
def hashtag_link(post):
    content = post.content #    #고양이 야옹 #강아지 멍멍 -> <a>#고양이</a>로 바꿔주겠다.
    hashtags = post.hashtags.all() # QuerySet [HashTag object (1:고양이), HashTag object (2:강아지)...]

    for hashtag in hashtags:
        content = content.replace(
            f'{hashtag.content}', 
            f'<a href="/posts/hashtags/{hashtag.id}/">{hashtag.content}</a>'
        )
    return content
```

