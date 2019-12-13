# 191211_Algorithm_계절학기__순열,조합,부분집합

### 순열, 조합

```python
# 순열1
def perm(n, k):
    if n==k:
        print(p[:3])
    else:
        for i in range(n, m):
            p[n], p[i] = p[i], p[n]
            perm(n+1, k)
            p[n], p[i] = p[i], p[n]
            
# 순열2
def perm2(n, k, a):
    if n==k:
        print(a[:3])
    else:
        for i in range(m):
            if not used[i]:
                used[i] = 1
                perm2(n+1, k, a+[p[i]])
                used[i] = 0

# 조합
def comb(n, k, a, l):
    if n==k:
        print(a[:3])
    else:
        for i in range(l, m): # 시작범위 지정
            if not used[i]:
                used[i] = 1
                comb(n+1, k, a+[p[i]], i)
                used[i] = 0

m = 5
p = [1,2,3,4,5]
used = [0]*m
# perm(0, 3)
# perm2(0, 3, [])
comb(0, 3, [], 0)
```

### 부분집합

```python
# 부분수열 - 재귀(★)

# 1.비트연산 기본형
def f(n, k, S):
    global cnt
    if n == k:
        if sum(bit) != 0: # 공집합이 아닌 경우
            t = 0
            for i in range(k):
                if bit[i] == 1:
                    t += num[i]
            if t == S:
                cnt += 1
    else:
        bit[n] = 0
        f(n+1, k, S)
        bit[n] = 1
        f(n+1, k, S)

# 2.시간 훨씬 줄어듬(비트연산없이 바로 계산)
def f2(n, k, S, ts, m):
    global cnt
    if n == k:
        if m > 0 and ts == S:
            cnt += 1
    else:
        f2(n+1, k, S, ts, m)
        f2(n+1, k, S, ts+num[n], m+1)

N, S = map(int, input().split())
num = list(map(int, input().split()))
bit = [0] * N
cnt = 0
# f(0, N, S)
f2(0, N, S, 0, 0)
```

<br>

### ex01 스타트와 링크

<https://www.acmicpc.net/problem/15889>

```python
# bj 15889 스타트와 링크

# 1.부분집합 - 재귀(★)
def f(n, k):
    global res
    if n==k:
        a, b = 0, 0
        for ii in range(k//2-1):
            for jj in range(ii, k//2):
                a += arr[start[ii]][start[jj]] + arr[start[jj]][start[ii]]
                b += arr[link[ii]][link[jj]] + arr[link[jj]][link[ii]]
        res = min(res, abs(a-b))
    else:
        if len(start) < k//2: # start팀에 남은 자리가 있으면(★)
            start.append(n)
            f(n+1, k)
            start.pop()
        if len(link) < k//2: # link팀에 남은 자리가 있으면(★)
            link.append(n)
            f(n+1, k)
            link.pop()

# 2.부분집합 - 비트연산(더빠름)
def f2(k):
    global res
    for i in range(1<<k):
        start2, link2 = [], []
        for j in range(k):
            if i&(1<<j):
                start2.append(j)
            else:
                link2.append(j)
        if len(start2) == len(link2) and start2 < link2:
            a, b = 0, 0
            for ii in range(k//2-1):
                for jj in range(ii, k//2):
                    a += arr[start2[ii]][start2[jj]] + arr[start2[jj]][start2[ii]]
                    b += arr[link2[ii]][link2[jj]] + arr[link2[jj]][link2[ii]]
            res = min(res, abs(a-b))


N = int(input())
arr = [list(map(int, input().split())) for _ in range(N)]
start, link = [], []
res = 1000000000
f(0, N)
# f2(N)
print(res)
```

