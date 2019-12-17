# 191217_Algorithm_계절학기

```
# bj 17070 파이프옮기기
# bj 17135 캐슬 디펜스
# bj 15683 감시
```

### ex01 새로운 게임 2

<https://www.acmicpc.net/problem/17837>

```python
# bj 17837 새로운 게임2

# 쌓여있는 말도 함께 이동하며 방향은 변경되지 않음을 주의
# 변수명이 겹치는탓에 디버깅이 오래 걸렸다

def f():
    T = 0
    while 1:
        T += 1
        for idx, val in enumerate(A):
            i, j, d = val
            # 말번호로 순서찾기
            ii = arr2[i][j].index(idx)
            # 쌓인 말
            temp = arr2[i][j][ii:]
            ni, nj = i+dir[d][0], j+dir[d][1]
            if 0<=ni<N and 0<=nj<N:
                # 흰
                if arr[ni][nj] == 0:
                    # 인덱스로 이동한말 없애기
                    arr2[i][j] = arr2[i][j][:ii]
                    # 쌓인 말 위치 이동
                    arr2[ni][nj] += temp
                    # 말 위치정보도 변경
                    for t in temp:
                        i, j, d = A[t]
                        A[t] = [ni, nj, d]
                # 빨
                elif arr[ni][nj] == 1:
                    arr2[i][j] = arr2[i][j][:ii]
                    arr2[ni][nj] += temp[::-1]
                    for t in temp:
                        i, j, d = A[t]
                        A[t] = [ni, nj, d]
                # 파
                elif arr[ni][nj] == 2:
                    ni, nj = i+dir[C[d]][0], j+dir[C[d]][1]
                    if 0<=ni<N and 0<=nj<N:
                        # 흰
                        if arr[ni][nj] == 0:
                            arr2[i][j] = arr2[i][j][:ii]
                            arr2[ni][nj] += temp
                            # 현재말 먼저 방향 변경해주기(★)
                            # 아래에서 같은 변수명 d가 쓰이므로
                            A[idx] = [ni, nj, C[d]]
                            for t in temp[1:]:
                                i, j, d = A[t]
                                # 쌓인 말은 방향변경 X
                                A[t] = [ni, nj, d]
                        # 빨
                        elif arr[ni][nj] == 1:
                            arr2[i][j] = arr2[i][j][:ii]
                            arr2[ni][nj] += temp[::-1]
                            A[idx] = [ni, nj, C[d]]
                            for t in temp[1:]:
                                i, j, d = A[t]
                                A[t] = [ni, nj, d]
                        # 파
                        elif arr[ni][nj] == 2:
                            A[idx] = [i, j, C[d]]
                    else:
                        A[idx] = [i, j, C[d]]
            # 범위 밖
            else:
                ni, nj = i+dir[C[d]][0], j+dir[C[d]][1]
                if 0<=ni<N and 0<=nj<N:
                    # 흰
                    if arr[ni][nj] == 0:
                        arr2[i][j] = arr2[i][j][:ii]
                        arr2[ni][nj] += temp
                        A[idx] = [ni, nj, C[d]]
                        for t in temp[1:]:
                            i, j, d = A[t]
                            A[t] = [ni, nj, d]
                    # 빨
                    elif arr[ni][nj] == 1:
                        arr2[i][j] = arr2[i][j][:ii]
                        arr2[ni][nj] += temp[::-1]
                        A[idx] = [ni, nj, C[d]]
                        for t in temp[1:]:
                            i, j, d = A[t]
                            A[t] = [ni, nj, d]
                    # 파
                    elif arr[ni][nj] == 2:
                        A[idx] = [i, j, C[d]]
            if T > 1000:
                print(-1)
                return
            if 0<=ni<N and 0<=nj<N and len(arr2[ni][nj]) >= 4:
                print(T)
                return

dir = ((0,0),(0,1),(0,-1),(-1,0),(1,0))
C = (0,2,1,4,3)
N, K = map(int, input().split())
arr = [list(map(int, input().split())) for _ in range(N)] # 체스판(0흰1빨2파)
arr2 = [[[] for _ in range(N)] for _ in range(N)] # 말 저장
A = [list(map(int, input().split())) for _ in range(K)] # 말정보: 행, 열, 방향(1오2왼3위4아)

for idx, val in enumerate(A):
    i, j, d = val
    A[idx] = [i-1, j-1, d]
    arr2[i-1][j-1].append(idx) # 말번호로 저장
f()
```

<br>

### ex02 

<https://www.acmicpc.net/problem/12100>

```python

```

