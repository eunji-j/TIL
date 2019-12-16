# 191216_Algorithm_계절학기

### ex01 주사위 윷놀이

<https://www.acmicpc.net/problem/17825>

```python
# bj 17825 주사위 윷놀이

arr = [[0,1,2,3,4,5],[2,2,3,4,5,6],[4,3,4,5,6,7],[6,4,5,6,7,8],[8,5,6,7,8,9],
       [10,22,23,24,30,31],[12,7,8,9,10,11],[14,8,9,10,11,12],[16,9,10,11,12,13],
       [18,10,11,12,13,14],[20,25,26,30,31,32],[22,12,13,14,15,16],[24,13,14,15,16,17],
       [26,14,15,16,17,18],[28,15,16,17,18,19],[30,27,28,29,30,31],[32,17,18,19,20,21],
       [34,18,19,20,21,21],[36,19,20,21,21,21],[38,20,21,21,21,21],[40,21,21,21,21,21],
       [0,21,21,21,21,21],[13,23,24,30,31,32],[16,24,30,31,32,20],[19,30,31,32,20,21],
       [22,26,30,31,32,20],[24,30,31,32,20,21],[28,28,29,30,31,32],[27,29,30,31,32,20],
       [26,30,31,32,20,21],[25,31,32,20,21,21],[30,32,20,21,21,21],[35,20,21,21,21,21]]

def f(n, hap):
    global maxV
    if n == 10:
        maxV = max(maxV, hap)
    else:
        for i in range(4):
            temp = A[i]
            if A[i] == 21: continue
            else:
                for j in range(4):
                    if i != j and A[j] != 21:
                        if arr[A[i]][dice[n]] == A[j]: break
                else:
                    A[i] = arr[A[i]][dice[n]]
                    f(n+1, hap+arr[A[i]][0])
                    A[i] = temp

dice = list(map(int, input().split()))
A = [0, 0, 0, 0]
maxV = 0
f(0, 0)
print(maxV)
```

<br>

### ex02 2048(Easy)

<https://www.acmicpc.net/problem/12100>

```python
# bj 12100 2048(Easy)

def f2(i):
    global arr
    if i == 0: # 위
        arr2 = list(map(list, zip(*arr)))
        for j in range(N):
            k = 0
            while 1:
                if k >= N-1: break
                l = 1
                while 1:
                    if k+l >= N:break
                    if arr2[j][k+l] == 0:
                        l += 1
                    else:
                        if arr2[j][k] == arr2[j][k+l]:
                            arr2[j][k] *= 2
                            arr2[j][k+l] = 0
                            k += l+1
                            l = 1
                        else:
                            k += l
                            l = 1
                k += 1
            s = 0
            for k in range(N):
                if arr2[j][k]:
                    arr2[j][s] = arr2[j][k]
                    if k != s:
                        arr2[j][k] = 0
                    s += 1
        arr = list(map(list, zip(*arr2)))
    elif i == 1: # 아래
        arr2 = list(map(list, zip(*arr)))
        for j in range(N):
            k = N-1
            while 1:
                if k <= 0: break
                l = 1
                while 1:
                    if k-l < 0: break
                    if arr2[j][k-l] == 0:
                        l += 1
                    else:
                        if arr2[j][k] == arr2[j][k-l]:
                            arr2[j][k] *= 2
                            arr2[j][k-l] = 0
                            k -= l+1
                            l = 1
                        else:
                            k -= l
                            l = 1
                k -= 1
            s = N-1
            for k in range(N-1, -1, -1):
                if arr2[j][k]:
                    arr2[j][s] = arr2[j][k]
                    if k != s:
                        arr2[j][k] = 0
                    s -= 1
        arr = list(map(list, zip(*arr2)))
    elif i == 2: # 왼
        for j in range(N):
            k = 0
            while 1:
                if k >= N-1: break
                l = 1
                while 1:
                    if k+l >= N: break
                    if arr[j][k+l] == 0:
                        l += 1
                    else:
                        if arr[j][k] == arr[j][k+l]:
                            arr[j][k] *= 2
                            arr[j][k+l] = 0
                            k += l+1
                            l = 1
                        else:
                            k += l
                            l = 1
                k += 1
            s = 0
            for k in range(N):
                if arr[j][k]:
                    arr[j][s] = arr[j][k]
                    if k != s:
                        arr[j][k] = 0
                    s += 1
    elif i == 3: # 오
        for j in range(N):
            k = N-1
            while 1:
                if k <= 0: break
                l = 1
                while 1:
                    if k-l < 0: break
                    if arr[j][k-l] == 0:
                        l += 1
                    else:
                        if arr[j][k] == arr[j][k-l]:
                            arr[j][k] *= 2
                            arr[j][k-l] = 0
                            k -= l+1
                            l = 1
                        else:
                            k -= l
                            l = 1
                k -= 1
            s = N-1
            for k in range(N-1, -1, -1):
                if arr[j][k]:
                    arr[j][s] = arr[j][k]
                    if k != s:
                        arr[j][k] = 0
                    s -= 1

def f(n, num, l):
    global maxV, arr
    if n <= 5:
        maxV = max(maxV, num)
        for i in range(4):
            if l != i: # 백트래킹(★)
                ori = [arr[i][:] for i in range(N)]
                f2(i)
                temp = 0
                for j in range(N):
                    for k in range(N):
                        if arr[j][k] > temp:
                            temp = arr[j][k]
                f(n+1, temp, i)
                arr = [ori[i][:] for i in range(N)]


N = int(input())
arr = [list(map(int, input().split())) for _ in range(N)]
maxV = 0
f(0, 0, -1)
print(maxV)
```

