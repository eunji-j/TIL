### 1910110_Database_SQL, ORM

# SQL과 django ORM

## 기본 준비 사항

- https://bit.do/djangoorm에서 csv 파일 다운로드

- django app

  - `django_extensions` 설치

  - `users` app 생성

  - csv 파일에 맞춰 `models.py` 작성 및 migrate

    아래의 명령어를 통해서 실제 쿼리문 확인

    ```bash
    $ python manage.py sqlmigrate users 0001 # (app-name) (migration-name)
    # 지정한 마이그레이션의 SQL 내역
    ```

- `db.sqlite3` 활용

  - `sqlite3`  실행

    ```bash
    $ ls
    db.sqlite3 manage.py ...
    $ sqlite3 db.sqlite3
    ```

  - csv 파일 data 로드 

    ```sqlite
    sqlite > .tables -- db.sqlite3가 들고있는 테이블목록
    auth_group                  django_admin_log
    auth_group_permissions      django_content_type
    auth_permission             django_migrations
    auth_user                   django_session
    auth_user_groups            auth_user_user_permissions  
    users_user -- users의 user모델
    sqlite > .mode csv
    sqlite > .import users.csv users_user -- users의 user에 users.csv데이터를 집어넣는다.
    sqlite > SELECT COUNT(*) FROM user_users;
    100
    ```

- 확인

  - sqlite3에서 스키마 확인

    ```sqlite
    sqlite > .schema users_user
    CREATE TABLE IF NOT EXISTS "users_user" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "first_name" varchar(10) NOT NULL, "last_name" varchar(10) NOT NULL, "age" integer NOT NULL, "country" varchar(10) NOT NULL, "phone" varchar(15) NOT NULL, "balance" integer NOT NULL);
    ```

<br>

- ORM 실행 명령어

  ```bash
  $ python manage.py shell_plus
  ```

  

## 문제

> 아래의 문제들을 sql문과 대응되는 orm을 작성 하세요.
>
> - `sql`: `<appname>_<modelname>` 직접적인 DB접근 (사용할일 거의 없을 예정)
>
> - `orm`: `<classname>` 언어를 sql로 변환하여 DB접근
>
>   -  모든 언어마다 있다. (해당 언어로만 DB조작하고 싶을 때 편리, 속도상대적느림.)
>
>   - Object-Relational Mapping: 객체와 테이블을 매핑시켜준다. 매핑된 데이터는 `queryset` 형태로 넘어온다. = 순회가능, 리스트와 기능유사 [queryset문법](https://docs.djangoproject.com/en/2.2/ref/models/querysets/)

### 기본 CRUD 로직

1. 모든 user 레코드 조회

   ```python
   # orm
   User.objects.all()
   ```

   ```sql
   -- sql
   SELECT * FROM users_user;
   ```

2. user 레코드 생성

   ```python
   # orm (순서X)
   User.objects.create(
       last_name="홍", first_name="길동", age=100, 
       country="제주", phone="123-1234-1234", balance=123123)
   ```

   ```sql
   -- sql (순서O)
   INSERT INTO users_user
      ...> VALUES(101,"길동", "홍", 30, "제주도", "010-1234-1234", 10000);
   ```

   - 하나의 레코드를 빼고 작성 후 `NOT NULL` constraint 오류를 orm과 sql에서 모두 확인 해보세요.

3. 해당 user 레코드 조회

   ```python
   # orm
   User.objects.get(id=102)
   ```

   ```sql
   -- sql
   SELECT * FROM users_user WHERE id=102;
   ```

4. 해당 user 레코드 수정

   ```python
   # orm
   user = User.objects.get(id=102)
   user.age = 10
   user.save()
   ```

   ```sql
   -- sql
   UPDATE users_user SET last_name="김" WHERE id=102;
   ```

5. 해당 user 레코드 삭제

   ```python
   # orm
   User.objects.get(id=102).delete() # 연결하여 사용가능
   ```

   ```sql
   -- sql
   DELETE FROM users_user WHERE id=102;
   ```

### 조건에 따른 쿼리문

1. 전체 인원 수 

   ```python
   # orm
   User.objects.all().count() # 방법1 (all생략가능)
   len(User.objects.all()) # 방법2
   ```

   ```sql
   -- sql
   SELECT COUNT(*) FROM users_user;
   ```

2. 나이가 30인 사람의 이름

   ```python
   # orm
   User.objects.filter(age=30).values('first_name')
   
   # <QuerySet [{'first_name': '영환'}, {'first_name': '보람'}, {'first_name': '은영'}, {'first_name': '길동'}]>
   ```

   ```sql
   -- sql
   SELECT first_name FROM users_user WHERE age=30;
   ```

3. 나이가 30살 이상인 사람의 인원 수

   -  `l 은 less`, `g는 greater`, `t 는 than`, `e 는 equal` 을 의미합니다.

   ```python
   # orm
   User.objects.filter(age__gte=30).count()
   ```

   ```sql
   -- sql
   SELECT COUNT(*) FROM users_user WHERE age>=30;
   ```

4. 나이가 30이면서 성이 김씨인 사람의 인원 수

   ```python
   # orm
   User.objects.filter(age=30, last_name='김').count()
   ```

   ```sql
   -- sql
   SELECT COUNT(*) FROM users_user WHERE age=30 AND last_name='김';
   ```

5. 지역번호가 02인 사람의 인원 수

   ```python
   # orm
   User.objects.filter(phone__startswith='02-')
   ```

   ```sql
   -- sql
   SELECT COUNT(*) FROM users_user WHERE phone like '02-%';
   ```

6. 거주 지역이 강원도이면서 성이 황씨인 사람의 이름

   ```python
   # orm
   User.objects.filter(country='강원도', last_name='황').values('first_name')
   ```

   ```sql
   -- sql
   SELECT first_name FROM users_user WHERE country='강원도' AND last_name='황';
   ```



### 정렬 및 LIMIT, OFFSET

1. 나이가 많은 사람 10명

   ```python
   # orm
   User.objects.order_by('-age')[:10]
   ```

   ```sql
   -- sql
   SELECT * FROM users_user ORDER BY age DESC LIMIT 10;
   ```

2. 잔액이 적은 사람 10명

   ```python
   # orm
   User.objects.order_by('balance')[:10]
   ```

   ```sql
   -- sql
   SELECT * FROM users_user ORDER BY balance LIMIT 10;
   ```

3. 성, 이름 내림차순 순으로 5번째 있는 사람

   ```python
   # orm
   User.objects.order_by('-last_name', '-first_name')[4]
   ```

   ```sql
   -- sql
   SELECT * FROM users_user ORDER BY last_name DESC, first_name DESC LIMIT 1 OFFSET 4;
   ```



### 표현식

1. 전체 평균 나이

   ```python
   # orm
   from django.db.models import Avg, Min, Max, Sum
   User.objects.aggregate(Avg('age'))
   
   # {'age__avg': 28.247524752475247}
   ```

   ```sql
   -- sql
   SELECT AVG(age) FROM users_user;
   ```

2. 김씨의 평균 나이

   ```python
   # orm
   User.objects.filter(last_name='김').aggregate(Avg('age'))
   ```

   ```sql
   -- sql
   SELECT AVG(age) FROM users_user WHERE last_name='김';
   ```

3. 계좌 잔액 중 가장 높은 값

   ```python
   # orm
   User.objects.aggregate(Max('balance'))
   ```

   ```sql
   -- sql
   SELECT MAX(balance) FROM users_user;
   ```

4. 계좌 잔액 총액

   ```python
   # orm
   User.objects.aggregate(Sum('balance'))
   ```

   ```sql
   -- sql
   SELECT SUM(balance) FROM users_user;
   ```