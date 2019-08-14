# 190814_Algorithm_배열



#### 전치행렬 1

```python
arr = [[1,2,3],[4,5,6],[7,8,9]]
# 자리 바꾸기
for i in range(len(arr)):
    for j in range(len(arr[0])):
        if i < j:
            arr[i][j], arr[j][i] = arr[j][i], arr[i][j]
print(arr)
```

```
[[1,4,7],[2,5,8],[3,6,9]]
```

#### 전치행렬 2

```python
arr = [[0]*4 for i in range(4)]
print(arr)
# 행과 열이 같은 칸을 기준으로 오른쪽 영역
k = 1
for i in range(4):
    for j in range(4):
        if i < j:
            arr[i][j] = k
            k += 1
print(arr)
# 행과 열이 같은 칸을 기준으로 왼쪽 영역
k = 10
for i in range(4):
    for j in range(4):
        if i > j:
            arr[i][j] = k
            k += 10
print(arr)
```

<br>

#### 델타배열 1

> 이웃한 값끼리 뺀 절대값의 합은?

​				3(i-1, j+0)

2(i+0, j-1) 	(i, j)	0(i+0, j+1)

​				1(i+1, j+0)

```python
# 생략

# N x N에서 i, j의 이웃 ni, nj는....
di = [0, 1, 0, -1]
dj = [1, 0, -1, 0]

for k in range(4):
	ni = i + di[k]
    nj = j + dj[k]
    if ni >= 0 and ni < N and nj >= 0 and nj < N:
        # 존재하는 이웃인지 검사
        print(arr[ni][nj], end=' ')
```

<br>

#### 부분집합

> 뒤에서 더 자세히

```python
arr = [1,2,3]
bit = [0,0,0]

for i in range(2):
    bit[0] = i
    for j in range(2):
        bit[1] = j
        for k in range(2):
            bit[2] = k
            print(bit)
            for m in range(3):
                if(bit[m] != 0): # m번 원소가 부분집합에 포함되면
                    print(arr[m], end=' ')
            print()
```

```
[0, 0, 0]

[0, 0, 1]
3 
[0, 1, 0]
2 
[0, 1, 1]
2 3 
[1, 0, 0]
1 
[1, 0, 1]
1 3 
[1, 1, 0]
1 2 
[1, 1, 1]
1 2 3 
```

<br>

#### 검색

- 순차 검색

```python
def f(n, v, arr):
    for idx in range(N):
        if arr[idx] == V: # 키값을 찾으면
            return idx
    return -1 # 배열 안에서 키값을 찾지 못하면

# 개수 N, 키 V
N, V = map(int, input().split())
arr = list(map(int, input().split()))
r = f(N, V, arr)
print(r)
```

- 이진 검색

```python
for t in range(int(input())):
    p, a = map(int, input().split())
    cnt = 0
    
    left, right = 1, p
    while 1:
        x = int((left+right)/2)
        cnt += 1
        if x == a:
            break
        if x < a:
            left = x
        else:
            right = x
   
    print(f'#{t+1} {cnt}')
```

- 해쉬

