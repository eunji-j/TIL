## 200102_Database_SQL JOIN

[참고](https://dataschool.com)

[SQL 연습장](https://docs.google.com/spreadsheets/d/1OUHAnTPdx0ga8P1_HBm6WUuWs02tvV-31mgi__XmRbc/edit#gid=1849152573)

### 테이블목록

##### topic

| tid  | title    | description    | author_id |
| ---- | -------- | -------------- | --------- |
| 1    | HTML     | HTML is …      | 1         |
| 2    | CSS      | CSS is …       | 2         |
| 3    | Database | Database is .. | 1         |
| 4    | Oracle   | Oracle is …    | NULL      |

##### author

| aid  | name     | city   | profile_id |
| ---- | -------- | ------ | ---------- |
| 1    | egoing   | seoul  | 1          |
| 2    | leezche  | jeju   | 2          |
| 3    | blackdew | namhae | 3          |

##### profile

| pid  | title     | description    |
| ---- | --------- | -------------- |
| 1    | developer | developer is … |
| 2    | designer  | designer is …  |
| 3    | DBA       | DBA is ..      |




### 1. LEFT OUTER JOIN ★

```sql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid;
```

3개

```sql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid LEFT JOIN profile ON author.profile_id = profile.pid;
```

특정열만

```sql
SELECT tid, topic.title, author_id, name, profile.title AS job_title FORM topic LEFT JOIN author ON topic.author_id = author.aid LEFT JOIN profile ON author.profile_id = profile.pid;
```

조건 추가

```sql
SELECT tid, topic.title, author_id, name, profile.title AS job_title FORM topic LEFT JOIN author ON topic.author_id = author.aid LEFT JOIN profile ON author.profile_id = profile.pid WHERE aid = 1;
```



### 2. INNER OUTER JOIN ★

```sql
SELECT * FROM topic INNER JOIN author ON topic.author_id = author.aid;
```

3개

```sql
SELECT * FROM topic INNER JOIN author ON topic.author_id = author.aid INNER JOIN profile ON profile.pid = author.profile_id;
```



### 3. FULL OUTER JOIN

```sql
SELECT * FROM topic FULL OUTER JOIN author ON topic.author_id = author.id;
```



### 4. EXCLUSIVE LEFT JOIN

왼쪽에만 있는 값 (오른쪽은 NULL)

```sql
SELECT * FROM topic LEFT JOIN author ON topic.author_id = author.aid WHERE author.aid is NULL;
```

