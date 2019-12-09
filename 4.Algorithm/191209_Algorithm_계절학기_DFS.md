# 191209_Algorithm_계절학기__DFS

### DFS

```python
# DFS (그래프 경로)

# 1.조건
# 정점, 간선 개수 (Vertex, Edge)
# 간선 정보
# 방향 / 무방향
# 시작정점 0 / 1

# 2.해결
# 인접행렬 / 인접리스트
# 반복구조(스택) / 재귀

# 3.입력
# 7 8
# 1 2 1 3 2 4 2 5 4 6 5 6 6 7 3 7


def dfs1(n): # 1.반복구조(스택), 각 노드를 1번씩만 방문
    s = []
    s.append(n) # 방문할 정점을 저장 후 방문표시
    visited[n] = 1

    while s: # 방문하지 않은 정점이 있으면(갈림길에서 남겨놓은 정점이 있으면)
        n = s.pop()
        print(n, end=' ') # 탐색 순서를 출력
        for i in range(1, V+1):
            if arr[n][i] == 1 and not visited[i]: # i가 n에 인접하고 방문하지 않은 정점이면
                s.append(i) # 방문할 정점을 저장 후 방문표시
                visited[i] = 1

def dfs2(n): # 2.재귀, 각 노드를 1번씩만 방문
    print(n, end=' ') # 탐색 순서를 출력
    visited[n] = 1
    for i in range(1, V+1):
        if arr[n][i] == 1 and not visited[i]:  # i가 n에 인접하고 방문하지 않은 정점이면
            visited[i] = 1
            dfs2(i)


V, E = map(int, input().split())
arr = [[0]*(V+1) for _ in range(V+1)] # 시작정점 1일 때(V+1)
info = list(map(int, input().split()))

# 간선정보로부터 인접행렬에 만들기
for i in range(E):
    n1, n2 = info[2*i], info[2*i+1]
    arr[n1][n2] = arr[n2][n1] = 1 # 무방향 그래프일 때

visited = [0]*(V+1)
# dfs1(1) # 1 3 7 6 5 4 2
dfs2(1) # 1 2 4 6 5 7 3
```

<br>

### ex01

```python
# swea 4871

def f(S): # 반복구조(스택)
    global res
    s = []
    s.append(S)
    visited[S] = 1

    while s:
        S = s.pop()
        if S == G:
            res = 1
            return
        for i in range(1, V+1):
            if arr[S][i] == 1 and not visited[i]:
                s.append(i)
                visited[i] = 1

def f2(S): # 재귀
    global res
    if S == G:
        res = 1
    visited[S] = 1
    for i in range(1, V+1):
        if arr[S][i] == 1 and not visited[i]:
            visited[i] = 1
            f2(i)

for t in range(1, int(input())+1):
    V, E = map(int, input().split())
    arr = [[0]*(V+1) for _ in range(V+1)]
    for i in range(E):
        n1, n2 = map(int, input().split())
        arr[n1][n2] = 1
    S, G = map(int, input().split())

    visited = [0]*(V+1)
    res = 0
    f(S)
    print('#{} {}'.format(t, res))
```

<br>

### ex02

```python
# bj 2667

def f(i, j):
    global res
    s = []
    s.append((i, j))
    arr[i][j] = '0'
    cnt = 1
    while s:
        i, j = s.pop()
        for a, b in ((0,1),(1,0),(0,-1),(-1,0)):
            ni, nj = i+a, j+b
            if 0<=ni<N and 0<=nj<N and arr[ni][nj] == '1':
                arr[ni][nj] = '0'
                s.append((ni, nj))
                cnt += 1
    res.append(cnt)

N = int(input())
arr = [list(input()) for _ in range(N)]

res = []
for i in range(N):
    for j in range(N):
        if arr[i][j] == '1':
            f(i, j)

res.sort()
print(len(res))
for i in range(len(res)):
    print(res[i])
```
