## 190708_StartCamp_day1

#### *4차산업혁명*

> Computational Thinking
> 계산, 기억기능 > 문제해결 (알고리즘)
> 프로그램 = 명령어의 집합

<br>

## 교육과정

`python, HTML, CSS, SQL, ORM, flash, Django, javascript, Vue.js, Git Amazon web services`

<br>

## 파이썬 기본문법

#### 딕셔너리

```python
phonebook = {
	"key":"value",
    "key":"value"
}
```

#### for문 (반복문)

```python
for i in range(5):
  print(i)
```

#### if문 (조건문)

```python
if(True):
	print("")
elif(True):
    print("")
else:
    print("")
```

<br>

## 예시)

`random` 을 이용

#### 로또번호 생성

```python
import random

-1-
lotto = [] 
numbers = list(range(1, 46)) #리스트생성

a = random.sample(numbers, 6) #비복원추출(숫자6개)
print(sorted(a))

-2-
lotto = []
number = random.randrange(1,46)

for i in range(6):
	while number in lotto:  #비복원추출(숫자6개)
		number = random.randrange(1, 46)
	lotto.append(number)
print(sorted(a))
```

<br>

#### 전화번호 선택

```python
import random

phonebook = {
  "BBQ":"062-111-1111",  
  "교촌":"062-222-2222"
}
choice = random.choice(list(phonebook.keys()))

#print(f"{변수})
print(f"{choice} : {phonebook[choice]}") 
```
