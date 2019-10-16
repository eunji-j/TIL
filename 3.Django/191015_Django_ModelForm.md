# 191015_Django_ModelForm

<br>

## 1. Form (input태그 자동 생성) -> (X 잊어버리기)

> 데이터를 검증하기 위한 목적. create / update에 영향. (-> form.html 공유)
>
> form태그와 submit태그는 자동생성되지 않는다.

<br>

#### 1) form 생성하기

1. movies > `forms.py`  생성 후 폼 클래스 생성

```python
from django import forms

class MovieForm(forms.Form):
    title = forms.CharField(max_length=100)
    title_en = forms.CharField(
                    max_length=100,
                    label='영문제목',
                    widget=forms.TextInput(
                        attrs={
                            'placeholder': '영문제목을 입력해주세요.'
                        }
                    )
                    )
  audience = forms.IntegerField()
    open_date = forms.DateField(widget=forms.DateInput(attrs={'type': 'date'}))
    genre = forms.CharField(max_length=40)
    watch_grade = forms.CharField(max_length=50)
    score = forms.FloatField()
    poster_url = forms.CharField(widget=forms.Textarea) # widget: Textarea태그로 바꾼다.
    description = forms.CharField(widget=forms.Textarea)
```

#### 2) form 사용하기

1. movies > `views.py` 에서 임포트후 사용


### Create

```python
from django.shortcuts import render, redirect
from .forms import MovieForm
from IPython import embed
from .models import Movie

def create(request):
    if request.method=="POST":
        # POST방식일 때 입력한 값이 있는 폼을 만든다.
        form = MovieForm(request.POST)
        # form.is_valid(): 데이터 검증
        # ex) 데이터가 입력되지 않았거나 형태에 맞지 않은 데이터가 입력된 경우
        if form.is_valid():
            # embed(): 터미널에서 입력값 확인
            movie = Movie()
            # form.cleaned_data: 딕셔너리 형태(공백문자있으면 없애줌)
            movie.title = form.cleaned_data.get('title') 
            movie.title_en = form.cleaned_data.get('title_en')
            movie.audience = form.cleaned_data.get('audience')
            movie.open_date = form.cleaned_data.get('open_date')
            movie.genre = form.cleaned_data.get('genre')
            movie.watch_grade = form.cleaned_data.get('watch_grade')
            movie.score = form.cleaned_data.get('score')
            movie.poster_url = form.cleaned_data.get('poster_url')
            movie.description = form.cleaned_data.get('description')
            movie.save()
            return redirect('movies:index')
        # else:
        #     # 검증실패시 데이터 돌려보냄
        #     # context = {
        # 	  # 'form': form
        # 	  # }
        # 	  # return render(request, 'form.html', context)

    else:
        # GET방식일 때 비어있는 폼을 만든다.
        form = MovieForm()
    # 중복되는 코드(context생성)
    context = {
        'form': form
    }
    return render(request, 'form.html', context)
```

### Update

```python
from django.shortcuts import render, redirect, get_object_or_404
# object를 하나 가져오거나 아니면 404에러가 뜬다.
from .forms import MovieForm
from IPython import embed
from .models import Movie

def update(request, id):
    # 404에러: 잘못된 페이지요청임을 알려주기 위함.(사용자잘못) 
    # ex) /movies/100/update (100번 데이터가 없을 때)
    movie = get_object_or_404(Movie, id=id)
    if request.method=="POST":
        pass
    else:
        # GET방식일 때 딕셔너리 값이 입력되어있는 폼을 만든다.
        form = MovieForm(initial=movie.__dict__)
    context = {
        'form': form
    }
    return render(request, 'form.html', context)
```

#### 3) form 보여주기

1. movies > templates > `form.html` 에서 폼 보여주기

```html
{% extends 'base.html' %}
{% block body %}
  <!-- 경로이름에 따라 제목 다르게 설정하는 법 -->
  {% if request.resolver_match.url_name == "create_model_form" %}
    <h1>create</h1>
  {% else %}
    <h1>update</h1>
  {% endif %}
  <form action="" method="POST">
    {% csrf_token %}
    <!-- 모든 input태그를 p타입으로 묶어준다. -->
    {{form.as_p}}}

    <!-- title라벨과 태그만 보여준다. -->
    <!-- {{form.title.label_tag}}  -->
    <!-- {{form.title}}  -->
    <input type="submit">
  </form>
{% endblock %}
```

<br>

## 2. ModelForm -> 중요★

> 일반폼 : 직접 필드 정의, 위젯 설정이 필요
>
> 모델폼 : 모델과 필드를 지정하면 모델폼이 자동으로 폼 필드를 생성

<br>

#### 1) modelform 생성하기

1. movies > `forms.py`  생성 후 폼 클래스 생성

```python
class MovieModelForm(forms.ModelForm):
    open_date = forms.DateField(widget=forms.DateInput(attrs={'type': 'date'}))
    
    class Meta: 
        model = Movie
        # 전체 필드 추가
        fields = '__all__'

class CommentModelForm(forms.ModelForm):
    class Meta:
        model = Comment
        # 특정 필드 추가
        fields = ('content',)
```

#### 2) modelform 사용하기

1. movies > `views.py` 에서 임포트후 사용

### Create

```python
def create_model_form(request):
    if request.method=="POST":
        form = MovieModelForm(request.POST)
        if form.is_valid():
            movie = form.save()
            return redirect('movies:detail', movie.id)
    else:
        form = MovieModelForm()
    context = {
        'form': form
    }
    return render(request, 'form.html', context)
```

### Update

```python
def update_model_form(request, id):
    movie = get_object_or_404(Movie, id=id)
    if request.method=="POST":
        form = MovieModelForm(request.POST, instance=movie)
        if form.is_valid():
            form.save()
            return redirect('movies:detail', id)
    else:
        form = MovieModelForm(instance=movie)
    context = {
        'form': form
    }
    return render(request, 'form.html', context)
```

### Create (Comment)

```python
def comment_create(request, id):
    movie = get_object_or_404(Movie, id=id)
    if request.method == "POST":
        form = CommentModelForm(request.POST)
        if form.is_valid():
            # commit=False: 아직 movie를 입력해주지 않았기 때문에 잠깐 임시저장한다.
            comment = form.save(commit=False)
            comment.movie = movie
            comment.save()
            return redirect('movies:detail', id)
        else:
            pass
    else:
        pass
```

<br>

## 3. bootstrap4

#### 1) 사용전 설정하기 [bootstrap4](https://pypi.org/project/django-bootstrap4/feeds)

1. `pip install django-bootstrap4` 설치

2. settings.py > INSTALLED_APPS에 `bootstrap4` 추가

3. base.html 맨위에 `{% load bootstrap4 %}` 작성 및 아래 코드추가

   ```html
     <head>
     	{% bootstrap_css %}
     </head>
     ...
     <body>
         {% bootstrap_javascript jquery='full' %}
     </body>
   ```

4. form.html 맨위에도 `{% load bootstrap4 %}` 작성 및 부트스트랩 적용

   ```HTML
   {% extends 'base.html' %}
   {% load bootstrap4 %}
   {% block body %}
   <!-- 경로이름에 따라 제목 다르게 설정하는 법 -->
   {% if request.resolver_match.url_name == "create_model_form" %}
   <h1>create</h1>
   {% else %}
   <h1>update</h1>
   {% endif %}
   <form action="" method="POST">
       {% csrf_token %}
       {% bootstrap_form form %}
       {% buttons submit="제출" %}{% endbuttons %}
   </form>
   {% endblock %}
   ```