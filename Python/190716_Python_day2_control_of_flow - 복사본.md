## 190716_Python_day2

### 주요학습내용

- **enumerate(iterable, start=0)**

  인덱스 접근 가능 (시작값 바꿀 수 있음)

  튜플형태로 존재

- **dictionary에서 `for` 활용하는 4가지 방법**

  ```python
  # 0. dictionary (key 반복)
  for key in dict:
      print(key)
  
  # 1. key 반복
  for key in dict.keys(): # 리스트형태
      print(key)
  
  # 2. value 반복    
  for val in dict.values(): # 리스트형태
      print(val)
  
  # 3. key와 value 반복
  for key, val in dict.items():
      print(key, val)
  ```

- **for - else**

  ```python
  # break가 안되는 상황을 만들어봅시다.
  for i in range(3):
      print(i)
      if i == 100:
          print(f'{i}에서 break')
          break
  else: # break를 통해 중간에 종료되지 않은 경우만 실행)
      print('break 실행 안됨')
  ```

  ```
  0
  1
  2
  break 실행 안됨
  ```

- **sum([리스트])**

  리스트 내 값들의 합