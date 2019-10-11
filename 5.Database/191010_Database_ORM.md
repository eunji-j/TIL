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
   
   # <User: User object (102)>
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
   
   # (1, {'users.User': 1})
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
   
   # 101
   ```

   ```sql
   -- sql
   SELECT COUNT(*) FROM users_user;
   
   101
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
   
   "영환"
   "보람"
   "은영"
   "길동"
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
   
   <QuerySet [<User: User object (1)>, <User: User object (4)>, <User: User object (28)>, <User: User object (53)>, <User: User object (65)>, <User: User object (26)>, <User: User object (55)>, <User: User object (58)>, <User: User object (74)>, <User: User object (82)>]>
   ```

   ```sql
   -- sql
   SELECT * FROM users_user ORDER BY age DESC LIMIT 10;
   
   1,"정호","유",40,"전라북도",016-7280-2855,370
   4,"미경","장",40,"충청남도",011-9079-4419,250000
   28,"성현","박",40,"경상남도",011-2884-6546,580000
   53,"상훈","홍",40,"전라북도",016-7698-6684,550
   65,"민서","송",40,"경기도",011-9812-5681,51000
   26,"영식","이",39,"경상북도",016-2645-6128,400000
   55,"미경","이",39,"경기도",02-6697-3997,890000
   58,"영일","배",39,"전라남도",010-3486-8085,280000
   74,"승민","배",39,"강원도",010-4833-9657,840
   82,"현지","김",39,"충청북도",02-8468-8321,680000
   ```

2. 잔액이 적은 사람 10명

   ```python
   # orm
   User.objects.order_by('balance')[:10]
   
   <QuerySet [<User: User object (99)>, <User: User object (48)>, <User: User object (100)>, <User: User object (5)>, <User: User object (24)>, <User: User object (61)>, <User: User object (92)>, <User: User object (46)>, <User: User object (38)>, <User: User object (60)>]>
   ```

   ```sql
   -- sql
   SELECT * FROM users_user ORDER BY balance LIMIT 10;
   
   99,"우진","성",32,"전라북도",010-7636-4368,150
   48,"보람","이",28,"강원도",02-2055-4138,210
   100,"재현","김",25,"경상북도",016-1252-2316,210
   5,"영환","차",30,"충청북도",011-2921-4284,220
   24,"숙자","권",33,"경상남도",016-4610-3200,230
   61,"우진","고",15,"경상북도",011-3124-1126,300
   92,"미경","박",35,"경상북도",010-5203-5705,300
   46,"명자","김",23,"전라남도",011-3545-5608,330
   38,"준호","심",28,"충청북도",016-6703-7656,340
   60,"은영","김",30,"경상북도",02-5110-2334,350
   ```

3. 성, 이름 내림차순 순으로 5번째 있는 사람

   ```python
   # orm
   User.objects.order_by('-last_name', '-first_name')[4]
   
   # <User: User object (101)>
   ```

   ```sql
   -- sql
   SELECT * FROM users_user ORDER BY last_name DESC, first_name DESC LIMIT 1 OFFSET 4;
   
   -- 101,"길동","홍",30,"제주도",010-1234-1234,10000
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
   
   -- 28.2475247524752
   ```

2. 김씨의 평균 나이

   ```python
   # orm
   User.objects.filter(last_name='김').aggregate(Avg('age'))
   
   # {'age__avg': 28.782608695652176}
   ```

   ```sql
   -- sql
   SELECT AVG(age) FROM users_user WHERE last_name='김';
   
   -- 28.7826086956522
   ```

3. 계좌 잔액 중 가장 높은 값

   ```python
   # orm
   User.objects.aggregate(Max('balance'))
   
   # {'balance__max': 1000000}
   ```

   ```sql
   -- sql
   SELECT MAX(balance) FROM users_user;
   
   -- 1000000
   ```

4. 계좌 잔액 총액

   ```python
   # orm
   User.objects.aggregate(Sum('balance'))
   
   # {'balance__sum': 14435040}
   ```

   ```sql
   -- sql
   SELECT SUM(balance) FROM users_user
   
   -- 14435040
   ```