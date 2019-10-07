# 191007_Django_Asked

## Asked 익명질문 사이트 만들어보기

- #### 기본 구성

```
* Project / App name
  - Project: asked
  - App: questions
  
* Models (class)
  - Question 질문 속성: content(내용)
  - Answer 댓글 속성: content(내용), question(질문번호: 외래키, cascade옵션)
  
* 페이지(.html) 구성
  - base.html (부모 html)
  - form.html (Question작성)
  - index.html (QnA목록)
  - detail.html (ToDo상세정보: 수정, 삭제)
  
* 화면 Navbar 구성 (base.html)
  1. All: ToDo목록 보여주는 메뉴
  2. New: ToDo등록하는 메뉴
```

<br>

## 새로 배운 것

### 1. 외래키(ForeignKey)

- 1:N (질문:답) 기능 추가하기

  > questions -> models.py

  ```python
  # 댓글 모델
  class Answer(models.Model):
      content = models.CharField(max_length=100)
      # 하나의 Question에 관계된다.
      question = models.ForeignKey(Question, on_delete=models.CASCADE)
  ```

- **django-extensions** 설치 후 사용하기 -> 터미널에서 파이썬코드로 DB조작

  ```
  $ pip install django-extensions
  ```

  ```python
  INSTALLED_APPS = [
  	'django_extensions',
  ]
  ```

  ```
  $ python manage.py shell_plus # 사용하기
  ```

  #### 1) 게시물 불러오기 (READ)

  ```python
  In [6]: question = Question.objects.get(id=1) 
      # 꼭 있는 데이터만 찾아야함.(아니라면 오류발생)
  ```

  #### 2) 댓글 작성하기 (CREATE)

  ```python
  # 첫번째 방법
  In [7]: answer = Answer() # Answer 인스턴스 생성
  In [11]: answer.content = '이것은 댓글입니다.' # content 변수 지정
  
  In [12]: answer
  Out[12]: <Answer: Answer object (None)> 
          # question변수 지정을 안해줘서 None값이 나온다.(fk는 필수로 값이 있어야 함.)
          # None은 값이 없는 상태이고 DB에 저장되지 않음.
          # 이상태로 저장하면 IntegrityError: NOT NULL constraint failed 에러 발생
          
  In [15]: answer.question = question # question 변수 지정 
  In [19]: answer.save() # 저장
      
  In [20]: answer
  Out[20]: <Question: Question object (1)>
  
  # 두번째 방법
  In [23]: Answer.objects.create(content='두번째', question=question)
  Out[23]: <Answer: Answer object (2)>
  ```

  #### 댓글 정보

  ```python
  In [25]: answer.question
  Out[25]: <Question: Question object (1)>     
  
  In [26]: answer.id
  Out[26]: 1
  In [27]: answer.pk # primary key(고유값) = id와 같다
  Out[27]: 1
  
  In [28]: answer.question_id # answer가 가지고 있는 question의 id값 (속도 좀더 빠름)
  Out[28]: 1
  In [30]: answer.question.id # question이 가지고 있는 id값 (타고들어감)
  Out[30]: 1
      
  In [32]: answer.delete() # answer 인스턴스 삭제
  In [45]: dir(Answer) # Answer 클래스가 쓸 수 있는 method목록(디렉토리)
  ```

  ### 1:N ★

  - Qustion(1) -> Answer(N) : `answer_set`

    ```python
    In [36]: question.answer_set.all()
    Out[36]: <QuerySet [<Answer: Answer object (1)>, <Answer: Answer object (2)>]>
    ```

    - `question.answer`로는 가져올 수 없다.
    - 항상 복수라고 생각 (몇개가 있는지 모르기때문에 1개일 때도 QuerySet이라는 리스트형태로 존재한다.)

  - Answer(N) -> Question(1) : `question`

    ```python
    In [25]: answer.question
    Out[25]: <Question: Question object (1)>     
    ```

