# 191004_Django_ SQL

## 기본 Django 흐름 (+ 추가)

### 1. 기본구조 생성

프로젝트 생성 / 앱 생성 

```
$ django-admin startproject board .
$ django-admin startapp todos
```

board폴더 -> settings.py에 생성한 앱을 등록한다.

​	(-> 등록해주지 않는 경우 ModuleNotFoundError: No module named 'todosdjango' 에러 발생)

```python
INSTALLED_APPS = [
    'todos',
]
```

.gitignore 파일 생성 [gitignore.io](gitignore.io) (python, windows, visualstudiocode 입력후 복사붙여넣기)

<br>

### 2. 웹페이지 생성(기본구성)

board폴더 -> urls.py에 메인경로 path() 추가한다.

```python
from django.urls import path, include

urlpatterns = [
    path('todos/', include('todos.urls'))
    # include(): todos폴더가 모듈처럼 사용된다
]
```

todos폴더 -> urls.py에 메인경로 path() 추가한다.

```python
from django.urls import path
from . import views

urlpatterns = [
    # Read
    path('', views.index, name="index"),
]
```

todos폴더 -> views.py에 메인함수 index()를 작성한다. url - html 페이지 연결

​	(일반적으로 인자값은 명시적으로 request로 설정한다. 다른값도 가능)

```python
def index(request):
    return render(request, 'index.html', context)
```

todos폴더 -> templates폴더 (이름중요) 생성 후 그 안에 최상위 파일(base.html)과 상속받은 페이지(index.html 등)도 생성한다.

​	(보통 views.py 함수이름과 일치시킨다.)

> index.html 메인페이지(상속받음)

```html
{% extends 'base.html' %}
{% block body %}
  <h1>index</h1>
  {% endfor %}
{% endblock %}
```

<br>

### 3. 모델링

> 파이썬 <-> DB(sql) 중간 번역 과정
>
> - 파이썬: 규격을 통일, 데이터 저장하는 기능x, 계산기능 초점
>
> - DB: 영구저장용

todos폴더 -> models.py에서 모델을 생성한다.

```python
class Todo(models.Model):
    title = models.CharField(max_length=50) # 짧은 글(input형태)-> max_length 필수
    content = models.TextField() # 긴 글(textarea형태)
    due_date = models.DateField()
    author = models.CharField(max_length=50)
```

모델링 명령어 

​	(모델이 바뀔때마다 해줘야한다. 왠만하면 바꾸지 않아야하고, 바꾸더라도 데이터 모두 날리고 하는 게 좋음.)

```
$ python manage.py makemigrations -> DB(sql)에 적용하기 위한 변환 명령어
$ python manage.py migrate -> 표 만들어주는 명령어
```

<br>

깃에 선택한 파일만 추가(add)하는 법

```
$ git add todos/migrations/0001_initial.py
$ git add todos/models.py
$ git add db.sqlite3
```

```
$ git status -> 임시저장 상태확인
$ git log --oneline -> 커밋 기록
```

<br>

### 4. CRUD

- 작성 순서: **.html** (사용자페이지) -> **urls.py** (경로연결) -> **views.py** (데이터처리)

#### todos폴더 -> urls.py (CRUD 합친 최종)

```python
from django.urls import path
from . import views

######################################################
app_name = 'todos'
# 앱마다 이름을 설정해준후 변수명(name)으로 path(라우팅) 설정
# 앱이 여러개일 때 더 편리하다.
######################################################

urlpatterns = [
    # Read
    path('', views.index, name="index"),
    # Create
    path('new/', views.new, name="new"),
    path('create/', views.create, name="create"),
	
    path('add/', views.add, name="add"),
    # Delete
    path('<int:id>/delete/', views.delete, name="delete"),
    # Update
    path('<int:id>/update/', views.update, name="update"),
]
```

<br>

#### (1) Create (POST) : Restful API방식 적용전

> GET vs POST (method방식)
>
> : 정보가져오기 vs 데이터 서버가 처리
>
> : 항상 같은 값을 반환 vs 반환페이지 예측이 불가

todos -> templates -> new.html의 form태그에 경로를 지정하고 urls.py에 경로를 지정한다.

```html
{% extends 'base.html' %}

{% block body %}
<!-- {% url 앱이름:변수명 %} -->
  <form action="{% url 'todos:create' %}" method="post"> 
    <!-- action="" 생략가능하다. -->
    {% csrf_token %}
    <!-- 랜덤한 문자열토큰 생성하여 정상적인 페이지에서의 요청인지 확인 -->
    작성자 : <input type="text">
    제목 : <input type="text">
    내용 : <input type="text">
    마감일 : <input type="date">
    <input type="submit" value="저장">
  </form>  
{% endblock %}
```

todos폴더 -> **views.py**에서 new, create함수로 데이터 저장한다.

```python
from django.shortcuts import render, redirect
from .models import Todo

def new(request):
    return render(request, 'new.html')

def create(request):
    author = request.POST.get('author') # querydict타입
    title = request.POST.get('title') 
    content = request.POST.get('content')
    due_date = request.POST.get('due-date')

    # todo = Todo(author=author, title=title, content=content, due_date=due_date)
    # todo.save()
    
    # 다른 방법(save()내포)
    todo = Todo.objects.create(
        author=author, 
        title=title, 
        content=content, 
        due_date=due_date
        )
    return redirect('/todos/')
```

#### (1.2) Create (POST) : Restful API방식 적용후 ★

> 하나의 url로 GET/POST 분기처리
>
> HTML5에서는 DELETE/PUT은 지원하지 않는다.

todos폴더 -> **views.py**에서 add()함수로 new, create 합체한다.

```python
###########################################################
def add(request):
    if request.method == "POST":
        author = request.POST.get('author')
        title = request.POST.get('title') 
        content = request.POST.get('content')
        due_date = request.POST.get('due-date')

        todo = Todo.objects.create(
            author=author, 
            title=title, 
            content=content, 
            due_date=due_date
            )
        return redirect('todos:index') # /todos/와 같은 기능
    else:
        return render(request, 'add.html')
###########################################################
```

> admin 관리자 생성 (Username, Password 필수값)

```
$ python manage.py createsuperuser
```

<br>

#### (2) Read

todos폴더 -> views.py에서 index.html에 모든 데이터 context에 담아보낸다.

```python
def index(request):
    todos = Todo.objects.all()
    context = {
        'todos': todos,
    }
    return render(request, 'index.html', context)
```

todos폴더 -> templates -> index.html에서 모든 데이터 보여준다.

```html
{% extends 'base.html' %}
{% block body %}
  <h1>index</h1>
  {% for todo in todos %}
    <h5>{{todo.author}}/{{todo.title}}/{{todo.due_date}}</h5>
  {% endfor %}
{% endblock %}
```

<br>

#### (3) Delete

todos폴더 -> templates -> index.html에 삭제버튼 추가한다.

```html
{% extends 'base.html' %}
{% block body %}
  <h1>index</h1>
  {% for todo in todos %}
    <h5>{{todo.author}}/{{todo.title}}/{{todo.due_date}}</h5>
    <a href="{% url 'todos:delete' todo.id %}">삭제</a>
    <!-- todo.id를 인자로 넣어줄 때 -->
  {% endfor %}
{% endblock %}
```

todos폴더 -> views.py에서 데이터 삭제

```python
def delete(request, id):
    todo = Todo.objects.get(id=id)
    todo.delete()
    return redirect('todos:index')
```

<br>

#### (4) Update (POST) : Restful API방식 적용후 ★

todos폴더 -> templates -> index.html에 수정버튼 추가한다.

```python
{% extends 'base.html' %}
{% block body %}
  <h1>index</h1>
  {% for todo in todos %}
    <h5>{{todo.author}}/{{todo.title}}/{{todo.due_date}}</h5>
    <a href="{% url 'todos:delete' todo.id %}">삭제</a>
    <a href="{% url 'todos:update' todo.id %}">수정</a>
    <!-- todo.id를 인자로 넣어줄 때 -->
  {% endfor %}
{% endblock %}
```

todos폴더 -> templates -> update.html의 form태그에 경로를 지정하고 urls.py에 경로를 지정한다.

```html
{% extends 'base.html' %}
{% block body %}
  <form action="{% url 'todos:update' todo.id %}" method="post">
    {% csrf_token %}
    <!-- 랜덤한 문자열토큰 생성하여 정상적인 페이지에서의 요청인지 확인 -->
    작성자 : <input type="text" name="author" value={{todo.author}}>
    제목 : <input type="text" name="title" value={{todo.title}}>
    내용 : <input type="text" name="content" value={{todo.content}}>
    마감일 : <input type="date" name="due-date" value={{todo.due_date|date:'Y-m-d'}}>
    <input type="submit" value="저장">
  </form>
{% endblock %}
```

todos폴더 -> **views.py**에서 add()함수로 데이터 수정한다.

```python
###########################################################
def update(request, id):
    todo = Todo.objects.get(id=id) # 반복되는 문장 if문 밖으로 빼준다.
    if request.method == "POST":
        author = request.POST.get('author')
        title = request.POST.get('title') 
        content = request.POST.get('content')
        due_date = request.POST.get('due-date')

        todo.author = author
        todo.title = title
        todo.content = content
        todo.due_date = due_date
        todo.save()
        return redirect('todos:index')
    else:
        context = {
            'todo': todo
        }
        return render(request, 'update.html', context)
###########################################################
```

