# 190816_Algorithm_문자열



#### 문자열 저장

```python
s = input() # Algorithm algo
print(s)
s =  input().split()
print(s)
s = list(input())
print(s)
```

```
Algorithm algo
['Algorithm', 'algo']
['A', 'l', 'g', 'o', 'r', 'i', 't', 'h', 'm', ' ', 'a', 'l', 'g', 'o']
```

<br>

#### 문자열 뒤집기

```python
s = list(input()) # ABCD
# ['A', 'B', 'C', 'D']

for i in range(0, len(s)//2): # 절반길이만큼 앞뒤 자리 바꿈
    s[i], s[len(s)-1-i] = s[len(s)-1-i], s[i]

# 1.for문 출력
for i in range(0, len(s)):
    print(s[i], end='')
print()

# 2.join()으로 출력
print(''.join(s))
```

```
DCBA
DCBA
```

<br>

#### 문자열 비교

> 함수를 이용

```python
def f(s1, s2):
    i = 0 # 비교할 글자의 인덱스
    while(i < len(s1) and i < len(s2)): # 어느 한 글자의 끝이 아니고
        if s1[i] == s2[i]: 
            i += 1
        else:
            break
    if i == len(s1) and i == len(s2): # 마지막 글자까지 같고 글자수 같으면 동시에 종료
        return 1
    else:
        return 0
    
s1 = input()
s2 = input()

if s1 == s2:
    print(1)
else:
    print(0)
    
print(f(s1, s2))
```

```
ABCD
ABCDE
0
```

<br>

#### 문자로 받은 숫자('123')를 숫자(123)로 바꿔서 출력

```python
def atoi(s): # 100의 자리 + 10의 자리 + 1의 자리
    n = 0
    for i in range(0, len(s)):
        n = n * 10
        # n = n + int(s[i])
        n = n + ord(s[i]) - ord('0') # 아스키코드 이용
    return  n

s = input() # '123'
r = atoi(s)
print(r)
```

```
123
123
```
