# 191002_Django_ reCRUD

## 기본 Django 흐름

#### 기본구조 생성

프로젝트 생성

앱 생성

프로젝트 settings.py에 앱 등록

gitignore 생성

<br>

#### 웹페이지 생성

맨 처음 urls.py에 새로운 경로(path)를 만들어준다. import include

views.py에 기능수행(CRUD)하는 함수를 만들어준다. url - html 페이지 연결

일반적으로 인자값은 명시적으로 request로 설정한다. (다른값도 가능)

templates 폴더 (이름중요) 안에 최상위 파일만든후 상속받은 페이지도 만들어준다. 보통 views.py 함수이름과 일치시킨다.

<br>

#### 모델 생성

파이썬 <-> DB(sql) 중간 번역 과정

파이썬: 규격을 통일, 데이터 저장하는 기능x, 계산기능 초점

DB: 영구저장용

```bash
$ python makemigrations 
# DB(sql)에 적용하기 위한 변환 명령어
# 등록된 app에 변경된 내용이 있는 경우 migration파일이 생성된다.
$ python migrate 
# 테이블 구조(표) 만들어주는 명령어
```

<br>

#### urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    # Read
    path('', views.index),
    # Create
    path('new/', views.new),
    path('create/', views.create),
    # Delete
    path('<int:id>/delete/', views.delete),
    # Update
    path('<int:id>/edit/', views.edit),
    path('<int:id>/update/', views.update),
]
```

<br>

#### Create

> 사용자 입력페이지

```html
{% extends 'base.html' %}

{% block body %}
  <form action="/todos/create/">
    <input type="text" name="title">
    <input type="date" name="duedate">
    <input type="submit">
  </form>
{% endblock %}
```

> views.py 데이터 저장

```python
from django.shortcuts import render, redirect
from .models import Todo

def create(request):
    title = request.GET.get('title')
    due_date = request.GET.get('duedate')

    todo = Todo() # 오브젝트 -> 인스턴스화
    todo.title = title # 어트리뷰트 설정
    todo.due_date = due_date
    todo.save()
    
    return redirect('/todos/')
```

> ​	admin 관리자 생성 (Username, Password 필수값)

```
$ python manage.py createsuperuser
```

<br>

#### Read

> 모든 데이터 리스트

```python
def index(request):
    todos = Todo.objects.all()
    context = {
        'todos': todos,
    }
    return render(request, 'index.html', context)
```



```html
{% extends 'base.html' %}
{% block body %}
  <h2>여기는 인덱스입니다.</h2>
  {% for todo in todos %}
    <h5>{{todo.due_date}} : {{todo.title}}</h5>
  {% endfor %}
{% endblock %}
```

<br>

#### Delete

```html
{% extends 'base.html' %}
{% block body %}
  <h2>여기는 인덱스입니다.</h2>
  {% for todo in todos %}
    <h5>{{todo.due_date}} : {{todo.title}}</h5>
    <a href="/todos/{{todo.id}}/delete/">삭제</a>
  {% endfor %}
{% endblock %}
```

> views.py 데이터 삭제

```python
def delete(request, id):
    todo = Todo.objects.get(id=id)
    todo.delete()
    return redirect('/todos/')
```

<br>

#### Update

> 사용자 수정페이지

```html
{% extends 'base.html' %}
{% block body %}
  <form action="/todos/update/">
    <input type="text" name="title" value={{todo.title}}>
    <input type="date" name="duedate" value={{todo.due_date|date:'Y-m-d'}}>
    <input type="submit">
  </form>
{% endblock %}
```

> views.py 데이터 수정

```python
def update(request, id):
    todo = Todo.objects.get(id=id)
    title = request.GET.get('title') # 기존 데이터
    due_date = request.GET.get('duedate')
    
    todo.title = title
    todo.due_date = due_date
    todo.save()
    
    return redirect('/todos/')
```