# 190816_Algorithm_주말예제



### 문제1

연속한 숫자가 증가하는 구간의 길이를 찾으려고 한다. 주어진 N개의 정수에서 가장 긴 증가 구간의 길이를 출력하는 프로그램을 만드시오. 증가 구간의 최소길이는 1이다.

예를 들어 4 5 1 2 3 의 경우 가장 긴 증가 구간은 1 2 3으로 길이는 3이 된다.

5 4 3 2 1의 경우 증가 구간의 최대 크기는 1이다.

첫 줄에 N, 다음 줄에 1000 이하인 N개의 양의 정수가 주어진다.

입력1

4 5 1 2 3 2 1

출력 1

3

```python
num = list(map(int, input().split()))
a = num[0]
cnt, res = 1, 0
for i in num[1:]:
    cnt = cnt + 1 if a < i else 1
    a = i
    res = max(res, cnt)
print(res)
```

<br>

### 문제2

산책길을 따라 N개 지점의 높이를 기록한 표를 만들었다. 산책길은 오르막 혹은 내리막으로 이뤄져 있고 오르막에서 내리막으로 바뀌는 부분을 봉우리라고 부르기로 했다. 주어진 산책로 정보에서 몇 개의 봉우리가 있는지 출력하는 프로그램을 만드시오. 맨 처음과 마지막 지점은 봉우리인지 판단할 수 없다.

첫 줄에 N, 다음 줄에 1000 이하인 N개의 양의 정수가 주어진다.

입력1

10

7 2 3 4 1 2 5 4 5 6

출력 1

2

[생각해보기]

- 골짜기의 개수는?
- 봉우리 사이에 다리를 놓으려 한다. 가장 긴 다리의 길이는? 봉우리가 한 개인 경우 0을 출력한다.

```python
num = list(map(int, input().split()))
a = num[1]
cnt = 0
for i in num[2:]:
    if a > i: cnt += 1
    a = i
print(cnt)
```

```python
num = list(map(int, input().split()))
a = num[0]
cnt = 0
for i in num[:-1]:
    if a > i: cnt += 1
    a = i
print(cnt)
```

```python
num = list(map(int, input().split()))
a = num[1]
cnt, res, leng = 0, 0, 1 # 봉우리개수, 가장긴다리, 현재 다리길이
for i in num[2:]:
    if cnt >= 1: # 봉우리가 1개 이상이면서
        if a < i: # 오르막이면
            leng += 1 # 다리길이 증가
        else: # 내리막이면
            res = max(res, leng) # 더 긴다리인지 검사
            leng = 1 # 다리길이 초기화
            cnt += 1 # 봉우리 개수 증가
    if a > i: cnt += 1
    a = i # 현재높이 대입
    if cnt < 2:
        res = 0
print(res)
```

<br>

### 문제3

NxN 형태의 숫자가 써진 숫자판이 있다. 이 중 짝수는 몇 개인지 알아내는 프로그램을 만드시오

첫 줄에 N, 다음 N개의 줄에 N개씩의 100 이하의 양의 정수가 주어진다. 1<=N<=10

입력 1

3

1 2 3

4 5 6

7 8 9

출력1

4

```python
cnt = 0
for i in range(int(input())):
    num = list(map(int, input().split()))
    for i in num:
        if i%2==0:
            cnt += 1
print(cnt)
```

<br>

### 문제4

0과 1이 들어있는 NxN 크기의 배열이 있다. 상하좌우 모두 0으로 둘러쌓인 1은 모두 몇 개인지 알아내는 프로그램을 만드시오. 반드시 상하좌우에 숫자가 존재하는 경우만 고려한다.

첫 줄에 N, 다음 줄부터 N줄에 걸쳐 N개의 숫자가 주어진다. 3<=N=10

입력 1

5

1 0 1 0 1

0 1 0 1 1

1 0 1 0 0

0 0 1 0 0

1 1 1 1 1

출력 1

1

```python
n = int(input())
a = [input().split() for i in range(n)]

cnt = 0
for i in range(1, n-1):
    for j in range(1, n-1):
        if a[i][j] == '1':
            if  a[i][j-1] == '0' and a[i][j+1] == '0' and a[i-1][j] == '0' and a[i+1][j] == '0':
                cnt += 1
print(cnt)
```

<br>

#### DP

### 문제5

NxN 구역의 높이를 기록한 표를 만들었다. 어떤 지역이 모든 이웃한 지역보다 높으면 봉우리라고 한다. 몇 개의 봉우리가 존재하는지 알아내는 프로그램을 만드시오. 이웃한 지역은 표에서 상하좌우, 양 대각선 방향을 포함해 모두 8곳이고, 이웃한 지역이 부족한 경우는 고려하지 않는다.

입력1

5

1 2 1 1 1

1 2 1 2 1

1 2 1 1 1

1 1 3 1 1

1 2 1 1 3

출력1

2

```python
n = int(input())
a = [input().split() for i in range(n)]

cnt = 0
for i in range(1, n-1):
    for j in range(1, n-1):
        num = a[i][j]
        if  a[i][j-1] < num and a[i][j+1] < num and a[i-1][j] < num and a[i+1][j] < num and a[i-1][j-1] < num and a[i-1][j+1] < num and a[i+1][j-1] < num and a[i+1][j+1] < num:
            cnt += 1
print(cnt)
```

