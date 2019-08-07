## 190807_Django_Board실습, CRUD

<br>

#### 1) Django 관리 페이지 (admin)

> http://127.0.0.1:8000/admin/

- 관리자 id/pw 만들기

  `$ python manage.py createsuperuser` -> id, email(생략가능), pw 설정

### 1. 사이트 등록

> todos폴더 > admin.py

```python
from django.contrib import admin
from .models import Todo
# 현재위치(.)의 models.py에 생성된 Todo 클래스 임포트

# Register your models here.
admin.site.register(Todo)
# admin페이지에 사이트 등록해주기
```

<br>

#### 2) Django Board 종합 실습

> ToDo(할일) 등록하고 관리하는 게시판 만들어 보는 시간 !! 

- Navbar구성
  - All (Todo목록)
  - New (Todo등록)
- Todo 클래스 속성
  - title (제목)
  - content (내용)
  - due_date (마감기간)
- 페이지(.html) 구성
  - new (Todo등록)  >  create (등록완료)
  - index (Todo목록)
  - detail (Todo상세정보: 수정, 삭제)
  - delete (삭제완료)
  - edit (수정)  >  update (수정완료)

<br>

### 1. 기본구조 작성

1. board 프로젝트 생성 및 `.gitignore` 생성(python, windows, visualstudiocode) [여기](https://gitignore.io)

   ```
   $ django-admin startproject board .
   ```

2. todos 앱 생성 및 board > settings.py > INSTALLED_APPS에 'todos' 추가

   ```
   $ django-admin startapp todos
   ```

   ```python
   INSTALLED_APPS = [
       'todos',
       'django.contrib.admin',
       'django.contrib.auth',
       'django.contrib.contenttypes',
       'django.contrib.sessions',
       'django.contrib.messages',
       'django.contrib.staticfiles',
   ]
   ```

3. board > urls.py > 임포트 및 path 추가

   ```python
   from django.contrib import admin
   from django.urls import path, include
   
   urlpatterns = [
       path('admin/', admin.site.urls),
       path('todos/', include('todos.urls')),
   ]
   ```

4. todos > urls.py > 임포트 및 path 추가

   ```python
   from django.urls import path
   from . import views
   
   urlpatterns = [
       # path('todos/'),
       path('new/', views.new),
       path('create/', views.create),
   ]
   ```

5. todos > views.py > `def new()`, `def create()` 추가

   ```python
   from django.shortcuts import render
   
   # Create your views here.
   def new(request):
       return render(request, 'new.html')
   def create(request):
       return render(request, 'create.html')
   ```

6. todos > templates 폴더 생성 > .html 파일 작성 (base, new, create 3개)

<br>

### 2. model 선언

1. todos > models.py > **class Todo 생성** (변수: title, content, due_date)

   ```python
   from django.db import models
   
   # Create your models here.
   class Todo(models.Model):
       title = models.CharField(max_length=50)
       content = models.CharField(max_length=200)
       due_date = models.DateField()
   ```

<br>

### 3. Create & Admin

<img src="assets/new.png" width="700px">

1. ORM(테이블 스키마) 변환 명령어 2개 실행

   ```
   $ python manage.py makemigrations
   $ python manage.py migrate
   ```

2. todos > admin.py > Todo 클래스 임포트 및 관리자 페이지에 사이트 등록

   ```python
   from django.contrib import admin
   from .models import Todo
   
   # Register your models here.
   admin.site.register(Todo)
   ```

3. todos > views.py > `def create()` 생성 > **새로운 데이터 생성 및 저장**

   ```python
   def create(request):
       title = request.GET.get('title')
       content = request.GET.get('content')
       due_date = request.GET.get('due-date')
       
       # sql 새로운 데이터 생성하는 코드
       # todo = Todo()
       # todo.title = title
       # todo.content = content
       # todo.due_date = due_date
       # todo.save()
   
       todo = Todo(title=title, content=content, due_date=due_date)
       todo.save()
   
       return render(request, 'create.html')
   ```

<br>

### 4. index page

<img src="assets/all.png" width="700px">

1. todos > urls.py > path 추가

   ```python
   urlpatterns = [
       # Read
       path('', views.index),
   ]
   ```

2. todos > views.py > `def index()` 생성 > **모든 데이터 불러오기**

   ```python
   def index(request):
       # 모든 데이터 불러오는 코드
       # queryset 형태로 보내줌(리스트랑 거의비슷)
       todos = Todo.objects.all()
       context = {
           'todos': todos,
       }
       return render(request, 'index.html', context)
   ```

3. todos > templates > `index.html` 생성 (**bootstrap > table 형태: for문**)

   ```html
   {% extends 'base.html' %}
   {% block body %}
     <table class="table table-dark mt-3">
       <thead>
         <tr>
           <th scope="col">#</th>
           <th scope="col">Title</th>
           <th scope="col">DueDate</th>
           <th scope="col">Buttons</th>
         </tr>
       </thead>
       <tbody>
         {% for todo in todos %}
           <tr>
             <th scope="row">{{todo.id}}</th>
             <td>{{todo.title}}</td>
             <td>{{todo.due_date}}</td>
             <td><button>상세보기</button></td>
           </tr>
         {% endfor %}
       </tbody>
     </table>
   {% endblock %}
   ```

<br>

### 5. detail

<img src="assets/detail.png" width="700px">

1. todos > urls.py > path 추가

   ```python
   urlpatterns = [
       # variable routing
       path('<int:todo_id>/', views.detail),
   ]
   ```

2. todos > views.py > `def detail()` 생성 > **해당 id의 데이터 불러오기**

   ```python
   def detail(request, todo_id):
       todo = Todo.objects.get(id=todo_id)
       context = {
           'todo': todo
       }
       return render(request, 'detail.html', context)
   ```

3. todos > templates > `detail.html` 생성 (bootstrap > jumbotron 형태: 수정, 삭제버튼)

   ```html
   {% extends 'base.html' %}
   {% block body %}
     <div class="jumbotron my-3">
       <h1 class="display-4">{{todo.title}}</h1>
       <p class="lead">{{todo.content}}</p>
       <hr class="my-4">
       <p>{{todo.due_date}}</p>
       <a class="btn btn-warning btn-lg" href="#" role="button">수정</a>
       <a class="btn btn-danger btn-lg" href="#" role="button">삭제</a>
     </div>
   {% endblock %}
   ```

4. todos > templates > index.html > **href 경로 추가 (해당 id값 넘겨주기)**

   ```html
   <td><a href="/todos/{{todo.id}}/" class="btn btn-primary">글보기</a></td>
   ```

<br>

### 6. delete & update

<img src="assets/update.png" width="700px">

1. todos > urls.py > path 추가

   ```python
   urlpatterns = [
       # Update
       path('<int:todo_id>/edit/', views.edit),
       path('<int:todo_id>/update/', views.update),
       # Delete
       path('<int:todo_id>/delete/', views.delete),
   ]
   ```

2. todos > views.py > redirect 임포트 및 `def delete()`, `def edit()`, `def update()` 생성

   ```python
   from django.shortcuts import render, redirect
   # redirect 임포트
   
   def delete(request, todo_id):
       todo = Todo.objects.get(id=todo_id)
       todo.delete()
       # return render(request, 'delete.html')
       return redirect('/todos/')
   	# 삭제완료 페이지로 가지않고 삭제한 뒤 바로 전체목록 페이지로 가도록 설정(redirect)
   
   def edit(request, todo_id):
       todo = Todo.objects.get(id=todo_id)
       context = {
           'todo': todo
       }
       return render(request, 'edit.html', context)
   
   def update(request, todo_id):
       title = request.GET.get('title')
       content = request.GET.get('content')
       due_date = request.GET.get('due-date')
   
       # todo = Todo.objects.get(id=todo_id)
       # todo.title = title
       # todo.content = content
       # todo.due_date = due_date
       # todo.save()
   
       todo = Todo(id=todo_id, title=title, content=content, due_date=due_date)
       todo.save()
   
       # return render(request, 'update.html')
       return redirect(f'/todos/{todo_id}/')
   	# 수정완료 페이지로 가지않고 수정한 뒤 바로 전체목록 페이지로 가도록 설정(redirect)
   ```

- render / redirect 차이점?
  - `return render(request, 'update.html')` → 템플릿(.html)을 불러온다.
  - `return redirect('/todos/')` → Url로 이동한다. -> views 실행

3. todos > templates > `detail.html` > 수정, 삭제 **버튼 경로 지정**

   ```html
   <a class="btn btn-warning btn-lg" href="/todos/{{todo.id}}/edit/" role="button">수정</a>
   <a class="btn btn-danger btn-lg" href="/todos/{{todo.id}}/delete/" role="button" onclick="return confirm('삭제할꺼니?')">삭제</a>
   <!-- javascript 코드로 삭제확인 -->
   ```

4. todos > templates > `edit.html` 생성 (기존의 값 **input태그의 value**로 보여준다.)

   > edit.html

   ```html
   {% extends 'base.html' %}
   {% block body %}
     <form action="/todos/{{todo.id}}/update/" class="m-5">
       <div class="form-group">
         <label for="title">ToDo</label>
         <input type="text" class="form-control" id="title", name="title" value="{{todo.title}}">
         </div>
       <div class="form-group">
         <label for="content">Content</label>
         <input type="text" class="form-control" id="content" name="content" value="{{todo.content}}">
       </div>
       <div class="form-group">
         <label for="due-date">Due Date</label>
         <input type="date" class="form-control" id="due-date" name="due-date" value="{{todo.due_date|date:'Y-m-d'}}">
        <!-- |date:'Y-m-d': Date타입 input value 설정해주는 법 -->   
       </div>
       <button type="submit" class="btn btn-warning">수정</button>
     </form>
   {% endblock %}
   ```

------

< ?! >

- todos = Todo.objects.**order_by('due_date')**.all()

  : 모든 데이터 가져올 때 정렬기준 정해주는 방법

