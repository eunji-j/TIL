# 191028_Django_API서버

> 뮤직리스트 **api서버**를 만들어보자 -> **json**형태의 데이터로만 사용자에게 응답 (**html  X**)
>
> 데이터를 관리하는 `서버(백엔드)`만 다뤄본다. 
>
> 이 api서버를 통해 프론트엔드 개발자에게 보여주고 어떻게 개발할지 소통할 수 있다.

<br>

## 1)  django REST framework

> [공식사이트](https://www.django-rest-framework.org/)

### 1. 환경설정

```python
pip install djangorestframework
```

api > `settings.py` > INSTALLED_APPS

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

데이터를 json형태로 변환해준다 -> `my_dumpdata.json` 파일 생성 (**복제**)

```bash
python manage.py dumpdata --indent 2 musics > my_dumpdata.json
```

데이터를 **불러와서 사용**한다. -> 모델링 다시 해줘야한다.

```bash
python manage.py loaddata musics/my_dumpdata.json
```

### 4. 경로설정 ★

>  뮤직리스트, 뮤직상세, 아티스트리스트, 아티스트상세, 댓글

api > `urls.py` 에서 api 서버 경로를 구성한다. (기존의 html방식과 다르다.)

```python
# api서버경로구성 -> 버전명시가 일반적(선택사항)
path('api/v1/', include('musics.urls')),
```

musics > `urls.py` 에서 각각의 경로를 설정한다.

```python
from django.urls import path
from . import views

app_name = 'musics'

urlpatterns = [
    # html이 아니기 때문에 name은 적지 않아도 된다.
    path('musics/', views.music_list, name='music_list'),
    path('musics/<int:id>/', views.music_detail, name='music_detail'),
    path('artists/', views.artist_list),
    path('artists/<int:id>/', views.artist_detail),
    path('musics/<int:id>/comments/', views.comment_create),
    path('comments/<int:id>/', views.comment_detail)
]
```

### 5. 직렬화 작업(serializer) ★

> 목적: 다른 환경과 데이터를 주고받으려면 동일한 데이터 구조를 가져야 하기 때문이다.

musics > `serializers.py` 파일 생성하고 공식사이트 튜토리얼 1에서 필요한 코드 복붙한다. 

- **직렬화해서 데이터를 관리**한다. 	ex) 가수1-노래1-댓글1, 가수1-노래1-댓글2, 가수1-노래1-댓글3 ...

```python
from rest_framework import serializers
from .models import Music, Artist, Comment

# ModelSerializer -> ModelForm과 구조가 유사하다.

class MusicSerializer(serializers.ModelSerializer):
    class Meta:
        model = Music
        fields = ('id', 'title', 'artist_id',)

# Artist 전체리스트 보여줄때
class ArtistSerializer(serializers.ModelSerializer):
    # 외래키 필드 설정
    musics = MusicSerializer(source='music_set', many=True)
    class Meta:
        model = Artist
        fields = ('id', 'name',)

# Artist 세부리스트 보여줄때(컬럼 추가)
class ArtistDetailSerializer(serializers.ModelSerializer):
    # music_set = MusicSerializer(many=True)
    # 이름을 바꾸고 싶을 때 source에 적어주면 된다.
    musics = MusicSerializer(source='music_set', many=True)
    # 새로운 필드 생성
    musics_count = serializers.IntegerField(source='music_set.count')
    class Meta:
        model = Artist
        fields = ('id', 'name', 'musics', 'musics_count',)

class CommentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Comment
        fields = ('id', 'content', 'music_id',)
```

musics > `views.py` 요청에 따른 응답으로 데이터를 보내주는 함수를 작성한다.

```python
from django.shortcuts import render, get_object_or_404
from .models import Music, Artist, Comment
# rest_framework
from rest_framework.decorators import api_view
from .serializers import MusicSerializer, ArtistSerializer, ArtistDetailSerializer, CommentSerializer
from rest_framework.response import Response
# Create your views here.


# rest_framework 요청, 응답
@api_view(['GET'])
def music_list(request):
    musics = Music.objects.all()
    # serializer: 직렬화작업 후 데이터리턴
    serializer = MusicSerializer(musics, many=True) # 데이터 여러개
    return Response(serializer.data)

    # json형태로 바꿔주는 다른방법
    # from django.http import JsonResponse
    # json_musics = {}
    # for music in musics:
    #     json_musics[music.id] = music.title
    # return JsonResponse(json_musics)

@api_view(['GET'])
def music_detail(request, id):
    music = get_object_or_404(Music, id=id)
    serializer = MusicSerializer(music) # 데이터 한개
    return Response(serializer.data)

@api_view(['GET'])
def artist_list(request):
    artists = Artist.objects.all()
    serializer = ArtistSerializer(artists, many=True)
    return Response(serializer.data)

@api_view(['GET'])
def artist_detail(request, id):
    artist = get_object_or_404(Artist, id=id)
    serializer = ArtistDetailSerializer(artist)
    return Response(serializer.data)

@api_view(['POST'])
def comment_create(request, id):
    # request.POST가 아닌 request.data가 쓰인다.
    serializer = CommentSerializer(data=request.data)
    # raise_exception=True: 에러 체크(값이 없거나 빠졌을 때 알려준다.)
    if serializer.is_valid(raise_exception=True):
        # commit과 동일한 기능
        serializer.save(music_id=id)
    return Response(serializer.data)


# GET, POST, PUT/PATCH, DELETE 모두 사용가능하다.
# Read, Create, Update, Delete로 대응된다.

@api_view(['GET', 'PUT', 'DELETE'])
def comment_detail(request, id):
    comment = get_object_or_404(Comment, id=id)
    if request.method == 'GET':
        serializer = CommentSerializer(comment)
        return Response(serializer.data)
    elif request.method == 'PUT':
        serializer = CommentSerializer(data=request.data, instance=comment)
        if serializer.is_valid(raise_exception=True):
            serializer.save()
            # 업데이트된 결과를 반환(일반적)
            return Response(serializer.data)
            # 업데이트 메시지를 반환
            # return Response({'message':'업데이트!!!'})
    else:
        comment.delete()
        return Response({'message': '삭제되었습니다!'})

```

**postman google Chrome** > 확장프로그램 추가

> 요청을 보내면 api만 보내주는 것을 확인할 수 있다.

```python
GET -> 127.0.0.1:8000/api/v1/musics/  # 뮤직리스트
GET -> 127.0.0.1:8000/api/v1/musics/1  # 뮤직상세
```

<br>

## 2) drf-yasg

[깃허브사이트](https://github.com/axnsan12/drf-yasg)

### 1. 환경설정

```
pip install drf-yasg
```

api > `settings.py` > INSTALLED_APPS

```python
'drf_yasg',
```

api > `urls.py` 에서 drf_yasg 코드 복붙한다.

```python
from django.contrib import admin
from django.urls import path, include
# drf_yasg
from drf_yasg.views import get_schema_view
from drf_yasg import openapi

# drf_yasg
schema_view = get_schema_view(
   openapi.Info(
      title="Music API",
      default_version='v1',
    # 나머지는 있어도 없어도 상관없는 설명
    #   description="Test description",
    #   terms_of_service="https://www.google.com/policies/terms/",
    #   contact=openapi.Contact(email="contact@snippets.local"),
    #   license=openapi.License(name="BSD License"),
   ),
)

urlpatterns = [
    path('admin/', admin.site.urls),
    # api서버경로구성 -> 버전명시가 일반적(선택사항)
    path('api/v1/', include('musics.urls')),
    # drf_yasg
    # 다른 스타일의 페이지 2개 -> 프론트엔드 개발자에게 보여주고 어떻게 개발할지 소통한다.
    path('redocs/', schema_view.with_ui('redoc')),
    path('swagger/', schema_view.with_ui('swagger')),
]
```
