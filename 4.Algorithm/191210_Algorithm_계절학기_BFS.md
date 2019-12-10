# 191210_Algorithm_계절학기__BFS

### BFS

```python
# BFS (그래프 경로)

# 1.조건
# 정점, 간선 개수 (Vertex, Edge)
# 간선 정보
# 방향 / 무방향
# 시작정점 0 / 1

# 2.해결
# 인접행렬 / 인접리스트
# 반복구조(큐)

# 3.입력
# 7 8
# 1 2 1 3 2 4 2 5 4 6 5 6 6 7 3 7


# 반복구조(큐)
def bfs(n): # bfs(n, k) k번 노드에 도착하는지 확인하는 탐색
    q = []
    q.append(n) # 시작노드 인큐
    visited[n] = 1 # 인큐한 노드 표시
    cnt = 0

    while q: # 큐에 노드가 남아있으면 반복
        n = q.pop(0) # 디큐
        cnt += 1 # 방문한 노드의 개수(노드에 대한 처리 위치)
        # if n==k:
        #    return 1 # bfs(n, k) k번 노드에 도착한 경우
        for i in range(1, V+1):
            if arr[n][i] == 1 and not visited[i]: # i가 n에 인접하고 방문하지 않은 정점이면
                q.append(i)
                visited[i] = visited[n] + 1 # 방문표시와 거리정보를 동시 저장
    return cnt
    # return 0 # bfs(n, k) k번 노드에 도착하지 못한 경우

V, E = map(int, input().split())
arr = [[0]*(V+1) for _ in range(V+1)] # 시작정점 1일 때(V+1)
info = list(map(int, input().split()))

# 간선정보로부터 인접행렬에 만들기
for i in range(E):
    n1, n2 = info[2*i], info[2*i+1]
    arr[n1][n2] = arr[n2][n1] = 1 # 무방향 그래프일 때

visited = [0]*(V+1)
print(bfs(1)) # 7 방문한 노드의 개수
print(visited) # [0, 1, 2, 2, 3, 3, 4, 3] 거리정보
```

<br>

### ex01 DFS와 BFS

<https://www.acmicpc.net/problem/1260>

```python
# bj 1260 DFS와 BFS

def dfs(V):
    print(V, end=' ')
    visited[V] = 1
    for i in range(N+1):
        if arr[V][i] == 1 and not visited[i]:
            visited[i] = 1
            dfs(i)

def bfs(V):
    q = []
    q.append(V)
    visited[V] = 1
    print()
    while q:
        V = q.pop(0)
        print(V, end=' ')
        for i in range(N+1):
            if arr[V][i] == 1 and not visited[i]:
                visited[i] = 1
                q.append(i)


N, M, V = map(int, input().split())
arr = [[0]*(N+1) for _ in range(N+1)]
for i in range(M):
    n1, n2 = map(int, input().split())
    arr[n1][n2] = arr[n2][n1] = 1
visited = [0]*(N+1)
dfs(V)
visited = [0]*(N+1)
bfs(V)
```

<br>

### ex02

```python

```
