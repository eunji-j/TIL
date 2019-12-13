# 191213_Algorithm_계절학기

### ex01 퇴사

<https://www.acmicpc.net/problem/14501>

```python
# bj 14501 퇴사

def f(n, N, hap):
    global maxV
    maxV = max(maxV, hap)
    for i in range(n, N):
        if i+arr[i][0] <= N:
            f(i+arr[i][0], N, hap+arr[i][1]) # 선택

N = int(input())
arr = [list(map(int, input().split())) for _ in range(N)] # 기간, 수익
maxV = 0
f(0, N, 0)
print(maxV)
```

<br>

### ex02 연구소

<https://www.acmicpc.net/problem/14502>

```python
# bj 14502 연구소

def f():
    global maxV, temp, arr
    q = virus[:]
    arr2 = [arr[i][:] for i in range(N)]
    while q:
        i, j = q.pop(0)
        for a, b in ((0,1),(1,0),(0,-1),(-1,0)):
            ni, nj = i+a, j+b
            if 0<=ni<N and 0<=nj<M:
                if arr2[ni][nj] == 0:
                    arr2[ni][nj] = 2
                    q.append((ni, nj))
    cnt = 0
    for i in range(N):
        for j in range(M):
            if arr2[i][j] == 0:
                cnt += 1
    maxV = max(maxV, cnt)

N, M = map(int, input().split())
arr = [list(map(int, input().split())) for _ in range(N)]
possible = []
virus = []
for i in range(N):
    for j in range(M):
        if arr[i][j] == 0:
            possible.append((i, j))
        elif arr[i][j] == 2:
            virus.append((i, j))
maxV = 0
for i in range(len(possible)-2):
    for j in range(i+1, len(possible)-1): # 범위 주의(★)
        for k in range(j+1, len(possible)): # 범위 주의(★)
            arr[possible[i][0]][possible[i][1]] = 1
            arr[possible[j][0]][possible[j][1]] = 1
            arr[possible[k][0]][possible[k][1]] = 1
            f()
            arr[possible[i][0]][possible[i][1]] = 0
            arr[possible[j][0]][possible[j][1]] = 0
            arr[possible[k][0]][possible[k][1]] = 0
print(maxV)
```

