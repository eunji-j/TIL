# 190709_StartCamp_day2

#### 설치

- **파이썬 3.7.3**
- **Git bash** (명령프롬프트 기능) - 터미널 사용
- **vs code** (코드작성)

<br>

## 1. CLI (명령줄 인터페이스)

`ls` 	리스트 나열
`pwd` 	현재위치
`cd` .. ~	디렉토리 변경 (상위폴더/ 홈폴더 이동)

`mkdir` 	디렉토리 생성
`touch` 	파일 생성

`echo` 	문자열 출력
`rm` -r 	파일 지우기 (폴더 지우기)
`exit` 	터미널 종료
`code` .	현재폴더에서 vscode 실행
`mv`	파일 이동/ 이름 변경

<br>

## 2. Git Bash 실행

1. `$ mkdir` startcamp

2. `$ cd` startcamp

3. `$ code` .

<br>

## 3. vs code 환경설정

1. `Ctrl + Shift + P` - > `Terminal: Select Default Shell` - > `Git Bash` 선택
2. 확장프로그램  `Korean Language Pack ` 검색후 설치
3. 터미널 - > 새 터미널

<br>

## 4. 응용하기

### **1) 웹 크롤링**

​	[참고](https://m.blog.naver.com/PostView.nhn?blogId=kiddwannabe&logNo=221177292446&proxyReferer=https%3A%2F%2Fwww.google.com%2F)

#### 	**1. 웹페이지 열기**

`browser.py` 파일 생성

```python
import webbrowser

url = "https://search.naver.com/search.naver?sm=top_hty&fbm=1&ie=utf8&query="
my_keywords = ["iu", "bts", "winner", "샤이니"]
for my_keyword in my_keywords:
    webbrowser.open(url + my_keyword)
```

터미널에서 `$ python browser.py` 

<br>

#### 	**2. 코스피 지수 가져오기**

`kospi.py` 파일 생성

터미널에서 `pip install requests`,  `pip install bs4` 설치

```python
import requests
from bs4 import BeautifulSoup

response = requests.get("https://finance.naver.com/sise/").text #요청후 응답받기
soup = BeautifulSoup(response, 'html.parser')

#select(select_one 1개), find(find_all 복수) : list형태
kospi = soup.select_one("#KOSPI_now") 
print(kospi.text)
```

​	터미널에서 `$ python kospi.py` 실행

<br>

#### 	**3. 네이버 실시간 검색어 가져오기** (반복문)

`naver.py` 파일 생성

```python
import requests
from bs4 import BeautifulSoup

response = requests.get("https://naver.com").text

soup = BeautifulSoup(response, "html.parser")
naver = soup.select("#PM_ID_ct span.ah_k")

for i in range(10):
    print(f"{i+1}. {naver[i].text}")
```

터미널에서 `$ python naver.py` 실행

<br>

### **2) 더미 데이터 생성**

- `os` 이용

> `os.chdir(r"주소")` : 해당폴더로 들어감
>
> `os.listdir("주소")` : 해당위치의 리스트 나열 (list 형태)
>
> `os.rename (현재이름, 바꿀이름)` : 이름 변경
>
> `os.system(cmd)` : 명령어 실행

<br>

#### 	**1. 이름 텍스트파일 100개 생성하기**

`students` 폴더 생성 - >  `files.py` 파일 생성

터미널에서 `$ pip install faker` 설치

```python
from faker import Faker
import os #운영체제

f = Faker('ko_KR')

for i in range(100):
    filename = f"{i}_{f.name()}.txt"
    cmd = f"touch {filename}"
    os.system(cmd) #실행
```

터미널에서 `$ python files.py` 실행

터미널에서 `$ mv files.py ..` 파일 이동

<br>

#### 	**2. 전체 파일 이름 변경하기** (SAMSUNG_) 

`students` 폴더 - >  `file_change.py` 파일 생성

```python
import os

os.chdir(r"C:\Users\student\startcamp\students") #해당폴더 들어간다

for filename in os.listdir("."):
    os.rename(filename, "SAMSUNG_" + filename)
```

<br>

#### 	**3. 전체 파일 이름 수정하기 (SSAFY_ )**

```python
import os

os.chdir(r"C:\Users\student\startcamp\students")

for filename in os.listdir("."):
    os.rename(filename, filename.replace("SAMSUNG_", "SSAFY_"))
```

터미널에서 `$ python file_change.py` 실행

<br>

### **3) 크롤링 (반복문이용)**

#### 	**1. 환율 정보 (table크롤링)**

```python
import requests
from bs4 import BeautifulSoup

response = requests.get("https://finance.naver.com/marketindex/exchangeList.nhn").text
soup = BeautifulSoup(response, 'html.parser')

tr = soup.select('tbody > tr')
for r in tr:
    print(r.select_one('.tit').text.strip() + " : " + r.select_one('.sale').text)
```

<br>

## 5. 버전 관리 시스템

- **github.com** - 프로젝트 관리
- **gitlab.com** - CLI를 통해 관리할 것
- bitbucket.com

<br>

### **1) gitignore.io 접속**

1. `python`,  `visualstudiocode`,  `windows` 입력 후 생성

2. 전체 복사하여 `.gitignore` 파일에 복사하기

<br>

### 2) **gitbash로 올리기** (add - commit - push)

1. `$ git init` 

   .git 숨김파일 생성 (이 폴더를 git으로 관리 ->  master로 바뀜)

2. `$ touch .gitignore` 

   파일생성

   <br>

3. `$ git add .` 

   **커밋할 목록에 추가 (모든 폴더와 파일 )**

4. `$ git commit -m "내용"`

   **커밋 만들기**

   <br>

5. `$ git config --global user.email "계정"` 

   `$ git config --global user.name "이름"` 

   사용자정보 입력 (맨처음 1번만)

   <br>

6. `$ git remote add origin https://github.com/깃허브주소`

   주소 복사/ 붙여넣기 

7. `$ git push origin master`

   **새로 생성한 커밋들 반영하기**

<br>

## 6. 협업 (끝말잇기)

- `$ git clone 협업주소` 복제하기
- `$ git pull origin master` 변경사항 불러오기

