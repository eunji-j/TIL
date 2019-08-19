# 190819_Algorithm_스택



#### 스택

```python
# 크기가 정해진 리스트를 사용한 스택 구현
stack = [0] * 10
top = -1

# push(1)
top += 1
stack[top] = 1
# push(2)
top += 1
stack[top] = 2
# push(3)
top += 1
stack[top] = 3
# pop()
while top != -1: # 스택이 비어있지 않으면 반복
    r = stack[top]
    top -= 1
    print(r)

# append()를 사용한 스택
s = list()
s.append(10)
s.append(20)
s.append(30)
while len(s) != 0:
    print(s.pop())
```

```
3
2
1
30
20
10
```

<br>

#### 괄호 검사

```python
# 괄호의 짝이 맞으면 1 아니면 0 리턴
def f(txt):
    s = list() # 스택생성
    for i in range(len(txt)):
        if txt[i] == '(': # 여는 괄호면 push()
            s.append(txt[i])
        elif txt[i] == ')': # 닫는 괄호면 pop()해서 비교
            if len(s) == 0: # 스택이 비어있으면 오류
                return 0
            else: # 스택이 비어있지 않으면 여는 괄호 하나 꺼냄
                s.pop() 
    if len(s) != 0: # 스택에 여는 괄호가 남아있으면
        return 0
    else:
        return 1
txt = input()
print(f(txt))
```

```
()()((()))
1
((()((((()()((()())((())))))
0
```

<br>

#### 재귀함수(function call)

> 팩토리얼

```python
def fact(n):
    if n < 1:
        return 1
    return f"{n}*{fact(n-1)}"

print(fact(5))
```

```
5*4*3*2*1
```

> 피보나치

```python
def fibo(n):
    if n <= 1:
        return n
    return fibo(n-1) + fibo(n-2)

print(fibo(4))
```

```
3
```

<br>

#### Memoization(중복 줄여줌)

> 피보나치 1

```python
def fibo1(n):
    global memo
    if n >= 2 and len(memo) <= n: # 아직 fibo(n)이 계산되지 않은 경우
        memo.append(fibo1(n-1) + fibo1(n-2)) # 계산된 값 추가
    return memo[n] # 계산되어 있으면 리턴

memo = [0, 1]
N = 4
print(fibo1(N))
```

> 피보나치 2

```python
def fibo1(n):
    global memo
    if n >= 2 and memo[n] == 0:  # 아직 fibo(n)이 계산되지 않은 경우
        memo[n] = fibo1(n-1) + fibo1(n-2) # 계산된 값 추가
    return memo[n] # 계산되어 있으면 리턴

N = 4
memo = [0] * (N+1)
memo[0] = 0
memo[1] = 1
print(fibo1(N))
```

<br>

#### DP

> memoization을 사용하는 것보다 반복적 구조로  DP를 구현한 것이 성능 면에서 보다 효율적이다.

> 재귀적 구조는 내부에 시스템 호출 스택을 사용하는 오버헤드가 발생하기 때문에

> 피보나치

```python
N = 4
d = [0] * (N+1)
d[0] = 0
d[1] = 1
for i in range(2, N+1):
    d[i] = d[i-1] + d[i-2]

print(d[N])
```
