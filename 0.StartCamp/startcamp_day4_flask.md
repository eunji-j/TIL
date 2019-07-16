## 190711_StartCamp_day4

**- 주요학습내용 -**

- flask 프레임워크 설치
  1. 04.first_app 폴더에 `app.py` 파일생성 후 http://flask.pocoo.org 에서 flask is Fun 코드 붙여넣기
  2. `$ pip install flask`  (flask 설치)
  3. `$ FLASK_APP=파일명.py flask run`  (이곳에서 flask 실행시켜줘)
- flask 사용하기

- flask 템플릿
- 집합 자료형(set)

<br>

### 01.flask 사용하기

>   Django와 Flask 모두 Python 기반의 Web Framework로
>
>   Django의 경우에는 강력한 기능을 제공해 주어 사용이 편리한 대신, Framework 자체적으로 설계한 개발 패턴에서 크게 벗어날 수 없는 구조라면
>
>  Flask는 처음부터 주어진 기능이 없지만, 내가 원하는 설계 방향대로 framework를 구축해 나갈 수 있다는 점이 매력이라고 하겠다. 
>
> https://bluese05.tistory.com/44

<br>

기본 사용법을 알아보자.

- **@app.route("/")**

  **def 함수명():**

  ​		**return**

> app.py

```python
from flask import Flask
app = Flask(__name__)	# Flask 객체의 인자로 __name__사용

@app.route("/")
# @app.route("/") : 해당 url이 들어오면 다음을 실행함
# "/" : 서버주소 그자체(root), 보통 생략되어 있음
def hello():
    return "Hello World!"
	# """ 메시지 """ : 여러개의 메시지입력이 가능(비효율적)

if __name__ == '__main__':
    app.run(debug=True)
# 'python 파일명.py' 명령처럼 직접 실행되는 경우 서버구동이 가능해짐
```

종료일까지 남은 일수을 알아보자.
```python
import datetime

@app.route("/dday")
def dday():
    today = datetime.datetime.now()
    endday = datetime.datetime(2019,11,29)
    d = endday-today
    return f"1학기 종료까지 {d.days}일 남음!!"
```

<br>

예시1. 다른 파일에 실행시켜보자.

- return **render_template('파일명.html')**

> templates 폴더 생성 후 `index.html`  파일 생성한다.

> `!+Tab`  : html 구조 자동완성

> app.py

```python
from flask import Flask, render_template	# import시켜주기
app = Flask(__name__)

@app.route("/html_file")
def html_file():
    return render_template('index.html')
    # render_template('파일명.html') : 해당파일 실행
```

예시2. 변수를 사용해보자. (동적웹)

- @app.route("/greeting/**< 변수타입:변수명 >**")

  def 함수명(**변수명**):

```python
@app.route("/greeting/<string:name>") 
def greeting(name):
    return f"안녕하세요 {name}님!!"

# <변수이름지정> : url이 바뀔때마다 return값 달라짐 (동적웹)
```

<br>

### 02.템플릿 - [참고](https://opentutorials.org/module/3669/22003)

예시1. 템플릿에 변수 전송해보자.

- return render_template("파일명.html", **변수1=값1, 변수2=값2**)

>  templates 폴더안에 `cube.html`  파일 생성한다.

> app.py

```python
@app.route("/cube_html/<int:num>")
def cube_html(num):
    cube_num = num**3
    return render_template("cube.html", num_html=num, cube_num_html=cube_num)
    # "파일명.html"에 값 보내고 해당페이지 실행
```

jinja2 문법을 알아보자. [참고](https://snacky.tistory.com/7)

- **{{    }}** : 파이썬 변수나 표현식

- **{% %}** : for문, if문 등의 구문

> cube.html

```html
<body>
    <strong style="color:purple">{{num_html}}</strong> 의 세제곱은 <i>{{cube_num_html}}</i> 입니다.
    <!-- 중괄호 두개 사이에 파이썬 변수나 표현식을 담을 수 있다. (jinja문법) -->
</body>
```

> greeting.html

```html
<body>
    {% if name == "은지" %}
        <p style="color:purple">{{name}}</p>야 안녕!!
    {% else %}
        <p style="color:purple">{{name}}</p>님 안녕하세요!!!
    {% endif %}
  	<!-- endif/endfor -->
</body>
```

<br>

예시2. 이미지url 보내고 해당 파일을 열어보자. (딕셔너리)

> app.py

```python
import random
@app.route("/lunch")
def lunch():
    menu = {
        "짜장면":"https://dimg.donga.com/wps/ECONOMY/IMAGE/2017/04/14/83849831.3.jpg",
        "짬뽕":"http://recipe1.ezmember.co.kr/cache/recipe/2017/10/22/aaeb2a235b89ac305ba919e33da2e6331.jpg",
        "스파게티":"https://www.tefal.co.kr/medias/?context=bWFzdGVyfHJvb3R8MjUyMTF8aW1hZ2UvanBlZ3xoMmYvaGRlLzkzMzg2Mzc5MTAwNDYuanBnfDQ4MzFlN2NlZjMwOTIxNmJmNDE3NjBiZjU0NzA3Mzk5ZDE2YmIzZDk4NTUyOWUzNGM4MmZiN2UwODA1YmY1MGE"
    }
    menu_list = list(menu.keys()) 
    # ["짜장면", "짬뽕", "스파게티"] key값 list로 다 가져옴
    pick = random.choice(menu_list)
    img = menu[pick]
    return render_template("lunch.html", pick=pick, img=img)

```

> lunch.html

```html
<body>
    <h3>오늘의 점심은 {{pick}} 어떠세요??</h3>
    <img src="{{img}}" alt="">
</body>
```

<br>

예시3. 페이지 간에 값을 주고받아보자. (form 태그)

> 입력한 글자 아스키 아트로 바꿔주는 사이트 (영어만) : [여기](http://artii.herokuapp.com/)

> url + `/make?text=" "`

- user_input = **request.args.get("매개변수")**

-  <form action="url">
       <input type="text" name="매개변수">
       <input type="submit">
   </form>

> templates 폴더안에 `text.html`, `result.html`  파일 생성한다.

> app.py

```python
from flask import Flask, render_template, request 	# import시켜주기

@app.route('/text')
def text():
    return render_template("text.html")

import requests		# import시켜주기
@app.route('/result')
def result():
    raw_text = request.args.get("raw")	# argument
    url = "http://artii.herokuapp.com/make?text="
    response = requests.get(url + raw_text).text
    return render_template("result.html", response=response)
```

> text.html

```html
<body>
    <h1>입력한 글자를 아스키 코드로 바꿔줍니다.</h1>
    <form action="/result">
        <input type="text" name="raw">
        <input type="submit">
    </form>
</body>
```

> result.html

```html
<body>
    <h1>결과는 다음과 같습니다!</h1>
    <pre>{{response}}</pre>
</body>
```

<br>

예시4. 랜덤게임 만들어보자.

>  [ngrok](dashboard.ngrok.com) 설치 후 student 폴더에 압축풀기

`$ ./ngrok.exe http 5000`  입력후 주소를 Ctrl + 클릭하면 주소 생성된다.

<br>

★ 예시5. 로또 번호 추첨하기

- **집합 자료형 이용 : set(리스트 혹은 문자열)**

> app.py

```python
@app.route("/lotto")
def lotto():
    return render_template('lotto.html')

@app.route('/lotto_result')
def lotto_result():
    # 사용자가 입력한 정보를 가져오기 1 2 3 4 5 6
    numbers = request.args.get("numbers").split()
    user_numbers = []
    for n in numbers:
        user_numbers.append(int(n))
    # user_numbers = [1,2,3,4,5,6]

    # 로또 홈페이지에서 정보를 가져오기 (json형태)
    url = "https://www.dhlottery.co.kr/common.do?method=getLottoNumber&drwNo=866"
    res = requests.get(url)
    lotto_numbers = res.json()  
    # res.json(): json(str타입)을 딕셔너리로 바꿔줌

    winning_numbers = []
    for i in range(1, 7):
        winning_numbers.append(lotto_numbers[f'drwtNo{i}']) 
    bonus_number = lotto_numbers['bnusNo']

    matched = len(set(user_numbers) & set(winning_numbers))
    # set(리스트 혹은 문자열): 집합 자료형 이용(순서가 없다, 중복x)
    # '&': 교집합
    # '|': 합집합
    # '-': 차집합
    
    if matched == 6:
        result = "1등"
    elif matched == 5:
        if bonus_number in user_numbers:    # if a in b: a가 b에 있으면
            result = "2등"
        else:
            result = "3등"
    elif matched == 4:
        result = "4등"
    elif matched == 3:
        result = "5등"
    else:
        result = "꽝"

    return render_template("lotto_result.html", u=user_numbers, w=winning_numbers, b=bonus_number, r=result)
```

> lotto.html

```html
<body>
    <form action="/lotto_result">
        <input type="text" name="numbers">
        <input type="submit">
    </form>
</body>
```

> lotto_result.html

```html
<body>
    {{u}}
    {{w}}
    {{b}}
    {{r}}
</body>
```