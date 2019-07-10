## 190710_StartCamp_day3

**- 주요학습내용 -**

- 사이트 가입
  - `slack` : QnA/ 채팅 목적
  - `trello` : 효율적 업무/ 역할분담/ 공유 목적

- 파일 조작 : txt, csv
- `HTML & CSS`
- `my page` 만들기

<br>

### 파일 조작 

먼저, `file` 폴더를 생성한다.

> git bash는 git에 업로드 되지 않은 폴더나 파일을 'U'라는 표시로 나타내준다.

> 많이 사용하지는 않을 것, 사용자 데이터는 파일보다는 DB에 저장할 것이다.

<br>

**.txt**파일 조작법을 알아보자.

> make_file.py

```python
# open("파일", 'w'): write
# open("파일", 'r'): read
# open("파일", 'a'): append

#1 옛날버전
f = open("student.txt", 'w') 
f.write("안녕하세요")
f.close()

#2 다른버전
with open("ssafy.txt", 'w') as f:	# 파일을 'f'로 지정해서 사용
	f.write("싸피 화이팅!!!")
```

크롤링을 응용해보자.

``` python
#3 환율정보 예시

import requests
from bs4 import BeautifulSoup

response = requests.get("https://finance.naver.com/marketindex/exchangeList.nhn").text
# 요청후 응답받기
soup = BeautifulSoup(response, 'html.parser')	
# 컴퓨터가 이해할 수 있게 변환

tr = soup.select('tbody > tr')
# select (여러개) : list타입
# select_one (1개)
# find, find_all

with open("exchange.txt", 'w') as f:
    for r in tr:
        f.write(r.select_one('.tit').text.strip() + " : " + r.select_one('.sale').text + "\n")
```

![exchange](C:\Users\student\TIL\0.StudyCamp\exchange.PNG)

<br>

**.csv** 파일로 작성해보자. (표형태)

> csv_file.py

```python
#4 .csv파일 작성하기

import csv

lunch = {
    "BBQ":"123123",
    "중국집":"789789",
    "한식":"456456"
} # 더미데이터 생성

with open("lunch.csv", 'w', encoding="utf-8", newline="") as f:     
    # utf-8: 한글호환성이 좋음, newline: 공백(오류방지)
    
    csv_writer = csv.writer(f)  
    # csv.writer("파일"): 분리된 문자열로 데이터 변환해줌

    for item in lunch.items():
        csv_writer.writerow(item)   # 한줄좀 써줘
```

한번 더 크롤링을 응용해보자.

> bithumb_csv.py

```python
#5 빗썸정보 예시

import csv
import requests
from bs4 import BeautifulSoup

response = requests.get("https://www.bithumb.com/").text
soup = BeautifulSoup(response, 'html.parser')

tr = soup.select('.coin_list > tr')
with open("bithumb.csv", 'w', encoding="utf-8", newline="") as f:
    csv_writer = csv.writer(f)
    for r in tr:
        row = [r.select_one('a > strong').text, r.select_one('.sort_real').text]    
        #한줄에 리스트로 저장하기
        csv_writer.writerow(row)
```

![bithumb](C:\Users\student\TIL\0.StudyCamp\bithumb.PNG)

<br>

### HTML & CSS 

`html&css` 폴더를 생성한다.



html 태그를 알아보자. [참고](https://www.w3schools.com/tags/default.asp)

> intro.html

```html
<!DOCTYPE html>
<html>
    <head>
        <!-- intro.css와 연결시켜줌 -->
        <link rel="stylesheet" href="./intro.css">
    </head>
    <body>
        <!-- HTML기본원칙 -->
        <!-- <태그이름 속성명="속성값" 속성명2="속성값2">내용</태그이름> -->
        
        <!-- <h1> to <h6> -->
        <h1>HTML</h1>
        <h1 class="blue">CSS</h1>
        <h2>HyperText Markup Language</h2>
        <!-- href: reference-->
        <a href="https://naver.com">네이버</a> 
        
        <h3>우리가 공부한 것</h3>
        <ol>
            <!-- 태그 안에 중첩이 가능하다 -->
            <li><strong><i>파이썬</i></strong></li>
            <li class="blue">HTML</li>

            <!-- CSS우선순위 : id > class > 태그 -->
            <!-- class는 여러개 적용이 가능하고, id는 한개만 가능하다. -->
            <li id="git" class="blue">Git</li>
        </ol>
    </body>
</html>
```

> intro.css

```css
/* 여기는 css파일입니다. */
h1 {
    background-color: red;
}
a {
    color: brown;
}
.blue {
    background-color: blue;
}
#git {
    background-color: yellow;
    font-size: 150%;
}
```

<br>

템플릿을 이용하여 `mypage`를 만들어보자.

- `Bootstrap` 반응형 웹 템플릿 : [startbootstrap](https://startbootstrap.com/themes/)
- 아이콘 지원 : [fontawesome](https://fontawesome.com/)

> [mypage](https://eunji-j.github.io/index.html)

<br>

#### **참고 사이트**

- 카카오 기술블로그
- 스포카 기술블로그
- 우아한형제들 기술블로그
- 강병욱

> 현업 개발자들의 글을 읽으며 체험해보자.
