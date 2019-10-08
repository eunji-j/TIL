# 191008_Django_Database

## 새로 배운 것

### 1. RDBMS (관계형 데이터베이스 관리 시스템)

- **Tuple = Entity (개체) : 행, 레코드**

- **Attribute (속성) : 열, 컬럼**

- SQL문법을 사용한다.

- SQL(관계형DB 이용) vs NoSQL(파일, 딕셔너리 등을 이용)

  - ex) SQLite: 서버가 아니라 파일에 넣고 빼는 방식으로 사용하는 비교적 가벼운 DB다. (NoSQL)

  - [데이터베이스 랭킹](db-engines.com/en/ranking)

- 스키마
  : 규격을 정하는 단계, 중간번역단계, 실제 데이터베이스는 아니지만 데이터베이스의 구조와 제약조건을 정의한 구조(명세서) -> 테이블로 구현한다.

- **PK (기본키)**
: 필수로 지정해야한다. 지정하지 않으면 자동으로 id값을 정해준다. 삭제되어도 새로운 id값이 부여된다. (unique, not null)

<br>

### 2. SQL (관계형 데이터베이스 관리 시스템)

- CRUD기능을 하는 특수 목적의 프로그래밍 언어
  - **DDL**(테이블, 스키마정의): CREATE, DROP, ALTER
  - **DML**(데이터 조작): **INSERT, UPDATE, DELETE, SELECT**★
  - DCL(권한제어): GRANT, REVOKE, COMMIT, ROLLBACK

<br>

### 3. SQLite3

http://bit.do/hello_db에서 

> 터미널에서 사용해보기(불편하다.)

```bash
$ sqlite3 # 실행
sqlite> .exit # 종료
```

```bash
$ sqlite3 test.sqlite3 # test.sqlite3파일을 실행하여 설치
sqlite> .databases # 데이터베이스 생성

sqlite> .mode csv # csv파일을 불러오는 모드로 변경한다.
sqlite> .import hellodb.csv examples(테이블명)
# hellodb.csv라는 파일을 examples라는 테이블로 불러온다.

sqlite> SELECT * FROM examples; # SQL문(키워드)-> ;을 붙여줘야 한다.
1,"길동","홍",600,"충청도",010-2424-1232

sqlite> .headers on # 좀더 보기 좋게 해준다.
sqlite> .mode column
sqlite> SELECT * FROM examples;
id          first_name  last_name   age         country     phone
----------  ----------  ----------  ----------  ----------  -------------
1           길동          홍           600         충청도         010-2424-1232
```

<br>

> 좀더 편리하게 파일에서 명령어 입력후 사용해보기★

### 4-1. intro

> 00_intro.sql

- ##### 기본

```sql
-- 데이터베이스 생성
.database

-- csv파일을 읽어오기
.mode csv
.import hellodb.csv examples

-- 예쁘게 보기
.headers on
.mode column

-- 테이블 조회
SELECT * FROM examples;
```
```bash
$ sqlite3 # 실행
sqlite> .read 00_intro.sql # 00_intro.sql파일을 읽는다.
```

<br>

### 4-2. DDL

> 01_DDL.sql

- ##### 기본

```sql
-- DDL(데이터 정의 언어)
-- 구조를 정의
CREATE TABLE classmates (
    id INTEGER PRIMARY KEY,
    name TEXT
);
 -- 테이블 목록 조회
.tables

-- 스키마 조회
.schema classmates

-- 테이블 삭제
DROP TABLE classmates;
.tables
```

```bash
sqlite> .read 01_DDL.sql
classmates  examples
CREATE TABLE classmates (
    id INTEGER PRIMARY KEY,
    name TEXT
);
examples
```

- ##### 예제

```sql
-- 예제
CREATE TABLE classmates (
    name TEXT,
    age INTEGER,
    address TEXT
);
.tables
.schema classmates
```

```bash
classmates  examples
CREATE TABLE classmates (
    name TEXT,
    age INTEGER,
    address TEXT
);
```

<br>

### 4-3. CRUD

> 02_CRUD.sql

- ##### 기본1

```sql
-- 테이블 구조 정의
CREATE TABLE classmates (
    name TEXT,
    age INTEGER,
    address TEXT
);
-- CREATE
INSERT INTO classmates (name, age, address) 
VALUES ('홍길동', 19, '광주');
-- READ
SELECT * FROM classmates;
-- CREATE

INSERT INTO classmates (name, address)
VALUES ('홍길동', '광주');
-- READ
SELECT * FROM classmates;

DROP TABLE classmates;
```

```bash
sqlite> .read 02_CRUD.sql
name        age         address
----------  ----------  ----------
홍길동         19          광주
name        age         address
----------  ----------  ----------
홍길동         19          광주
홍길동                     광주
```

- ##### 예제1

```sql
-- 예제1
CREATE TABLE classmates (
    name TEXT,
    age INTEGER,
    address TEXT
);
-- 데이터 컬럼순서가 맞아야한다.
-- 모든 열에 데이터를 넣을 때에는 컬럼을 생략해도 된다.
INSERT INTO classmates VALUES ('홍길동', 30, '서울');
-- rowid: PK를 작성하지 않았기 때문에 자동완성되는 PK(내부적으로 사용된다.)
SELECT rowid, * FROM classmates;

DROP TABLE classmates;
.tables
```

```bash
rowid       name        age         address
----------  ----------  ----------  ----------
1           홍길동         30          서울
examples
```

- ##### 예제2


```sql
-- 예제2: id 사용해서 테이블 만들기(오류확인★)
CREATE TABLE classmates (
    -- PK에 사용할 경우에는 INT(X), INTEGER(O)
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    age INT NOT NULL,
    address TEXT NOT NULL
);
INSERT INTO classmates VALUES (1, '홍길동', 29, '광주');
INSERT INTO classmates (name, age, address) VALUES ('홍길동', 29, '광주');
```

```bash
id          name        age         address
----------  ----------  ----------  ----------
1           김싸피         29          광주
2           김싸피         29          광주
```

- ##### 기본2

```sql
-- 기본제공되는 rowid 사용하기!!!(더 편리하다.)
CREATE TABLE classmates (
    name TEXT NOT NULL,
    age INT NOT NULL,
    address TEXT NOT NULL
);
-- 여러개 입력이 가능하다.
INSERT INTO classmates VALUES ('김철수', 23, '대전'), ('박나래', 23, '서울'), ('이요셉', 33, '구미');
```

#### SELECT

```sql
-- 특정컬럼 가져오기
SELECT rowid, name FROM classmates;
-- LIMIT(제한)
SELECT rowid, name FROM classmates LIMIT 1;
-- OFFSET(앞에서부터 건너뛰기)
SELECT rowid, name FROM classmates LIMIT 1 OFFSET 2; -- 2개 건너뛰고 1개 선택
SELECT rowid, name FROM classmates LIMIT 2, 3; -- 2개 건너뛰고 3개 선택
-- WHERE(조건)
SELECT rowid, name FROM classmates WHERE address='서울';

-- 전체
SELECT * FROM classmates;
-- DISTINCT(중복 제거)
SELECT DISTINCT age FROM classmates;
```

```bash
rowid       name
----------  ----------
1           김철수
2           박나래
3           이요셉
rowid       name
----------  ----------
1           김철수
rowid       name
----------  ----------
3           이요셉
rowid       name
----------  ----------
2           박나래
name        age         address
----------  ----------  ----------
김철수         23          대전
박나래         23          서울
이요셉         33          구미
age
----------
23
33
```

#### DELETE / UPDATE

```sql
-- DELETE
DELETE FROM classmates WHERE rowid=3;
INSERT INTO classmates VALUES ('김이박', 30, '광주');
SELECT rowid, * FROM classmates;

-- UPDATE
UPDATE classmates SET name='홍길동', address='제주' WHERE rowid='3';
SELECT rowid, * FROM classmates;

DROP TABLE classmates;
```

```bash
rowid       name        age         address
----------  ----------  ----------  ----------
1           김철수         23          대전
2           박나래         23          서울
3           김이박         30          광주
rowid       name        age         address
----------  ----------  ----------  ----------
1           김철수         23          대전
2           박나래         23          서울
3           홍길동         30          제주
```