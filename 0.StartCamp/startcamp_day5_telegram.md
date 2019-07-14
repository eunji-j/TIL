## 190712_StartCamp_day5

[TOC]

### 01. telegram 시작하기

- 05.telegram_bot 폴더에 `test.py` 파일과 `app.py` 파일 생성
- telegram에서 `botfather`과 채팅하여 botname과 botusername 입력하여 **토큰값** 받기

<br>

### 02. telegram 환경설정(Test)

기본 환경설정 해보자.

token과 chatid를 숨기기 (.env파일 생성)

> .env

```
TELEGRAM_TOKEN='토큰'
CHAT_ID='아이디'
```

`$ pip install python-decouple` 설치하고 임포트해주기

> test.py

```python
import requests
from decouple import config
token = config("TELEGRAM_TOKEN")
url = f"https://api.telegram.org/bot{token}/"
# <>를 {}로 바꿔주면 token 값을 유지한채로 url 생성함
user_id = config("CHAT_ID")

#1. telegram 주소 만들기
# send_url = f"{url}sendMessage?chat_id={user_id}&text=하이하이"
# requests.get(send_url)

#2. ngrok 주소 만들기 (외부에서 로컬서버 접속하게 해줌)
ngrok_url = "https://ee6dd9e7.ngrok.io"
webhook_url = f"{url}setWebhook?url={ngrok_url}/{token}"
print(webhook_url)

#3. pythonanywhere 주소 만들기
```

> .gitignore

```
.env
# gitignore 사이트에서 붙여넣기
```

<br>

### 03. telegram 챗봇 만들기

사용자와 챗봇간의 메시지 전달해보자.

> app.py

```python
from flask import Flask, request, render_template
from decouple import config
import requests
from bs4 import BeautifulSoup
import random
app = Flask(__name__)

api_url = "https://api.telegram.org"
token = config("TELEGRAM_TOKEN")
chat_id = config("CHAT_ID")
naver_id = config("NAVER_ID")
naver_secret = config("NAVER_SECRET")

# /write페이지에서 값 입력하고 챗봇에서 출력하는 방식
@app.route('/write')
def write():
    return render_template("write.html")

@app.route('/send')
def send():
    msg = request.args.get("msg")
    url = f"{api_url}/bot{token}/sendMessage?chat_id={chat_id}&text={msg}"
    res = requests.get(url)
    return render_template("send.html")

# 사용자와 챗봇간의 메시지 전달해보자
# POST 방식
@app.route(f"/{token}", methods=['POST'])
def telegram():
    print(request.get_json())
    data = request.get_json() 	# 추가됨
    user_id = data.get("message").get("from").get("id")
    user_msg = data.get("message").get("text")

    if data.get('message').get('photo') is None:
        if user_msg == "점심메뉴":
            menu_list = ["삼계탕", "철판낙지볶음밥", "물냉면"]
            result = random.choice(menu_list)
        elif user_msg == "로또":
            numbers = list(range(1,46))
            lotto = random.sample(numbers, 6)
            result = sorted(lotto)
        elif user_msg == "네이버차트":
            response = requests.get("https://music.naver.com/").text
            soup = BeautifulSoup(response, 'html.parser')

            chart = []
            songs = soup.select('tbody > tr')
            for i in range(10):
                name = songs[i].select_one('.name a').text
                artists = songs[i].select('._artist a')
                if len(artists) == 1:
                    artist = artists[0].text.strip()
                else:
                    artist = artists[0].text.strip() + ", " + artists[1].text.strip()
                chart.append(str(i+1) + ". " + name + " - " + artist)
            result = "\n".join(chart)   # 1줄씩 줄바꿈하기
        elif user_msg[0:2] == "번역":    # 문자열자르기
            # 번역 안녕하세요 저는 누구입니다.
            raw_text = user_msg[3:]
            papago_url = "https://openapi.naver.com/v1/papago/n2mt"
            data = {
                "source":"ko",
                "target":"en",
                "text":raw_text
            }   # 필수 요청변수들(source, target, text)
            header = {
                'X-Naver-Client-Id':naver_id,
                'X-Naver-Client-Secret':naver_secret
            }
            res = requests.post(papago_url, data=data, headers=header)
            translate_res = res.json()
            translate_result = translate_res.get('message').get('result').get('translatedText')
            result = translate_result 
        else:
            result = "지금 사용가능한 메시지는 [점심메뉴, 로또, 네이버차트, 번역]입니다."
    else:
        # 사용자가 보낸 사진을 찾는 과정
        result = "asdf"
        file_id = data.get('message').get('photo')[-1].get('file_id')
        # photo id 가져오기
        file_url = f"{api_url}/bot{token}/getFile?file_id={file_id}"
        file_res = requests.get(file_url)
        file_path = file_res.json().get('result').get('file_path')
        file = f"{api_url}/file/bot{token}/{file_path}"
        print(file)

        # 사용자가 보낸 사진을 클로버로 전송
        res = requests.get(file, stream=True)
        clova_url = "https://openapi.naver.com/v1/vision/celebrity"
        header = {
                'X-Naver-Client-Id':naver_id,
                'X-Naver-Client-Secret':naver_secret
            }
        clova_res = requests.post(clova_url, headers=header, files={'image':res.raw.read()})

        if clova_res.json().get('info').get('faceCount'):
            # 누구랑 닮았는지 출력
            celebrity = clova_res.json().get('faces')[0].get('celebrity')
            name = celebrity.get('value')
            confidence = celebrity.get('confidence')
            result = f"{name}일 확률이 {confidence * 100}%입니다."
        else:
            # 사람이 없음
            result = "사람이 없습니다."
        print(clova_res.text)

    res_url = f"{api_url}/bot{token}/sendMessage?chat_id={user_id}&text={result}"
    requests.get(res_url)

    return '', 200

if __name__=="__main__":
    app.run(debug=True)
```

<br>

### 04. 네이버 API 사용하기

- **POST 방식 전달**

> GET과 POST는 **웹 페이지에서 서버로 정보를 전송**하는 방식에 쓰인다.
>
> > `GET` : 요청 자체를 위한 정보를 전송할 때, 데이터 한계
>
> > `POST` : 서버 측에서의 정보 갱신 작업을 원할 때 사용

#### 파파고 NMT 번역 API

1. [developers.naver.com]() 에서 파파고 NMT API 탭 클릭

2. 애플리케이션 등록 후 발급받은 **Client ID**, **Client Secret** 값을 .env 에 붙여넣기(숨기기)

> .env

```
NAVER_ID=''
NAVER_SECRET=''
```

> app.py

```python
elif user_msg[0:2] == "번역":    # 문자열자르기
    # 번역 안녕하세요
    raw_text = user_msg[3:]
    papago_url = "https://openapi.naver.com/v1/papago/n2mt" 
    data = {
        "source":"ko",
        "target":"en",
        "text":raw_text
    }   # 필수 요청변수들(source, target, text)
    header = {
        'X-Naver-Client-Id':naver_id,
        'X-Naver-Client-Secret':naver_secret
    }
    res = requests.post(papago_url, data=data, headers=header)
    # POST방식으로 해당url에 요청값 전달
    
    translate_res = res.json()
    translate_result = translate_res.get('message').get('result').get('translatedText')
    # json() 방식으로 받아서 번역된 결과값 저장하기
    result = translate_result
```

#### Clova AI 얼굴인식 API

1. 내 애플리케이션 - > 사용 API 추가 (Clova Face Recognition)

> app.py

```python
else:
    # 사용자가 보낸 사진을 찾는 과정
    result = "asdf"
    file_id = data.get('message').get('photo')[-1].get('file_id')
    # photo id 가져오기
    file_url = f"{api_url}/bot{token}/getFile?file_id={file_id}"
    file_res = requests.get(file_url)
    file_path = file_res.json().get('result').get('file_path')
    file = f"{api_url}/file/bot{token}/{file_path}"
    print(file)

    # 사용자가 보낸 사진을 클로버로 전송
    res = requests.get(file, stream=True)
    clova_url = "https://openapi.naver.com/v1/vision/celebrity"
    header = {
        'X-Naver-Client-Id':naver_id,
        'X-Naver-Client-Secret':naver_secret
    }
    clova_res = requests.post(clova_url, headers=header, files={'image':res.raw.read()})

    if clova_res.json().get('info').get('faceCount'):
        # 누구랑 닮았는지 출력
        celebrity = clova_res.json().get('faces')[0].get('celebrity')
        name = celebrity.get('value')
        confidence = celebrity.get('confidence')
        result = f"{name}일 확률이 {confidence * 100}%입니다."
        else:
            # 사람이 없음
            result = "사람이 없습니다."
            print(clova_res.text)
```

<br>

### 05. telegram 챗봇 배포하기

> www.pythonanywhere.com 에서 files에 올리고 web에서 reload하여 관리 가능하다.

 telegram 챗봇