## 191118_Vue_CORS, JWT

## 로그인 과정 (vue + django)

### [JWT](https://jwt.io/) (Json Web Token) 

> vue에서는 jwt가 django의 세션ID와 동일한 기능을 한다.

client(vue) -> server(django)

​	1.로그인 

server(django) <-> data

​	2.유저확인

​	3.확인완료

4.JWT발급

5.응답(+JWT)

6.데이터요청(CRUD+JWT)

7.JWT검증

8.응답

<br>

# Todo App 만들기

### 1. `todo-project` > vue project 와 django project 생성

​	`vue`: 자동으로 gitignore와 처음commit이 남겨진다.

​	`django`: 둘다 직접 해줘야한다.

1. todo-front

```bash
$ vue create todo-front

# 서버실행
$ npm run serve
```

2. todo-back


```bash
$ mkdir todo-back
$ cd todo-back
$ django-admin startproject todoback .

# 서버실행
$ python manage.py runserver
```

<br>

### 2. `todo-front` > 라우터 설치

> 라우터 : 필요한 기능들만 적절하게 그룹핑 해주는 역할을 한다.

1. ui 서버 실행 후

```bash
$ vue ui
```

2. 플러그인 > `router` 검색하여 공식버전 설치 > 커밋(선택사항)하여 설치를 완료한다.



그 결과, src > router 와 views폴더가 생성된다.

- router > index.js 는 경로를 설정해주는 곳이다.

- views > About.vue 와 Home.vue 는 root가 바뀌지 않고 단일페이지가 바뀌는 일종의 컴포넌트라고 할 수 있다.

<br>

### 3. `todo-front` > 로그인 페이지 만들기

1. App.vue > 경로와 div설정

```vue
<template>
  <div id="app">
    <div id="nav">
      <!-- router-link: nav역할 -->
      <router-link to="/">Home</router-link>
      <router-link to="/login">Login</router-link>
    </div>
    <div class="container">
      <!-- router-view: 컴포넌트 위치하는곳 -->
      <router-view/>
    </div>
  </div>
</template>
```

2. src > components > `LoginForm.vue` 폼 생성

```vue
<template>
  <div>
    <!-- 로딩화면 -->
    <div v-if="loading" class="spinner-border" role="status">
      <span class="sr-only">Loading...</span>
    </div>
      
    <!-- 로그인화면 -->
    <div v-else class="login-div col-6 offset-3">
      <div class="form-group">
        <label for="id">ID</label>
        <input id="id" class="form-control" type="text" v-model="credential.username">
      </div>
      <div class="form-group">
        <label for="password">PASSWORD</label>
        <input id="password" class="form-control" type="password" v-model="credential.password">
      </div>
      <button class="btn btn-primary" @click="login">로그인</button>
    </div>
  </div>
</template>
```

3. src > views > `Login.vue` 에서 로그인폼 불러와서 사용

```vue
<template>
  <div class="login">
    <h1>로그인 페이지입니다.</h1>
    <LoginForm />
  </div>
</template>

<script>
import LoginForm from '../components/LoginForm.vue'
export default {
  name: 'Login',
  components: {
    LoginForm
  }
}
</script>
```

​	(참고) package.json > console 에러 잡기

```
"rules": {
      "no-console": "off"
    },
```

4. src > components > `LoginForm.vue` 에서 axios를 이용하여 **서버로 id, pw 보내기 (post 방식)**

```vue
<script>
import axios from 'axios'
export default {
  data: function(){
    return {
      // 사용자 ID, PW 들어있다.
      credential: {
        username: '',
        password: ''
      },
      loading: false
    }
  },
  methods: {
    login(){
      console.log('로그인 시도!!!')
      // 사용자 정보를 가지고 django로 jwt 토큰을 받을 수 있는 곳으로 요청보낸다.
      axios.post('http://localhost:8000/api-token-auth/', this.credential)
        .then((res)=>{
          // 로딩화면
          this.loading = true
          console.log(res)
        })
        .catch((e)=>{
          this.loading = true
          console.log(e)
        })
    }
  }
}
</script>
```

> vue, django 서버를 모두 실행후 로그인을 하면 **CORS 에러**가 난다. 포트가 다른 두곳이 충돌이 나기 때문이다.

<br>

### 4. `todo-back` > CORS, JWT설정

1. 설치

```bash
$ pip install djangorestframework
$ pip install djangorestframework-jwt
$ pip install django-cors-headers
```

2. todoback > `settings.py` 설정

```python
INSTALLED_APPS = [
    'todos',
    'rest_framework',
    'corsheaders',
    ...
]
MIDDLEWARE = [

	'corsheaders.middleware.CorsMiddleware',
]
...
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ),
}
...
JWT_AUTH = {
    'JWT_SECRET_KEY': SECRET_KEY,
    'JWT_ALGORITHM': 'HS256',
    'JWT_ALLOW_REFRESH': True,
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=3),
    # 만료된 토큰을 재발급
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=7),
}

CORS_ORIGIN_ALLOW_ALL = True
```

3. todoback > `urls.py` 에 추가

```python
from rest_framework_jwt.views import obtain_jwt_token

urlpatterns = [
    path('api-token-auth/', obtain_jwt_token),
]
```

4. todos > `models.py` 모델링

```python
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.
class User(AbstractUser):
    pass

class Todo(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=100)
    completed = models.BooleanField(default=False)
```

5. todoback > `settings.py` 에 유저모델 등록

```python
AUTH_USER_MODEL = 'todos.User'
```