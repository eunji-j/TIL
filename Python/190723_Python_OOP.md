## 190722_Python_OOP

<br>

### 01.주요 학습내용

#### 1. OOP with python (객체 지향 프로그래밍)

- 클래스
  - 속성(attribute)와 행위(behavior)를 정의한 것
  - 사용자 정의 데이터형

- 인스턴스
  - 클래스의 인스턴스/객체(실제로 메모리상에 할당된 것)이다.
  - 객체는 자신 고유의 속성(attribute)을 가지며 클래스에서 정의한 행위(behavior)를 수행할 수 있다.
- 속성
  - 클래스/인스턴스가 가지고 있는 속성(값)
- 메서드
  - 클래스/인스턴스가 할 수 있는 행위(함수)

> 특징: 캡슐화(파이썬에는 없다), `상속`, `추상화`, 다형성(pass)

<br>

#### 2. 클래스 및 인스턴스

>  **인스턴스 => 클래스 => 전역** 순으로 탐색을 한다.

> 특별한 상황을 제외하고는 **무조건 메서드에서 self를 첫번째 인자로 설정한다.**

- python 출력의 비밀 :  __repr__ 과 __str__

```python
# 특정 객체를 print() 할 때 보이는 값과
# 그냥 객체 자체가 보여주는 값도 사실 모두 우리가 바꿀 수 있습니다.
class Phone():
    def __str__(self):
        return 'print문에 넣으면 얘가 실행됩니다.'
    def __repr__(self):
        return '그냥 객체만 놔두면 얘가 나옵니다.'
```

```python
p = Phone()
print(p)
p
```

```
print문에 넣으면 얘가 실행됩니다.
그냥 객체만 놔두면 얘가 나옵니다.
```

<br>

#### 3. 용어 정리

```python
class Person:                     #=> 클래스 정의(선언) : 클래스 객체 생성
    name = 'unknown'              #=> 멤버 변수(data attribute)
    def greeting(self):           #=> 멤버 메서드(메서드)
        return f'{self.name}' 
richard = Person()      # 인스턴스 객체 생성
tim = Person()          # 인스턴스 객체 생성
tim.name                # 데이터 어트리뷰트 호출
tim.greeting()          # 메서드 호출
```

<br>

#### 4. 생성자 / 소멸자

```python
# 생성자와 소멸자를 만들어봅시다.
class Person:
    def __init__(self):
        print('응애')
    def __del__(self):
        print('RIP')
```

```python
# 생성해 봅시다.
p1 = Person()
# 생성해 봅시다.
p1 = Person()
```

```
응애
RIP
```

- 생성자에 `추가인자`를 받을 경우 생성시에 적어줘야 함. (그렇지 않으면 오류발생)

<br>

#### 5. 배운 것 응용해보기

- 포켓몬 구현하기

```python
class Pikachu(): # Pikachu 타입을 만든 것
    name = ''
    level = 5
    hp = level * 20
    exp = 0
   
	# 생성자
    def __init__(self, name):
        self.name = name
        print(f'피카츄 {self.name}가 생성되었습니다.')
    # 소멸자
    def __del__(self):
        print(f'피카츄 {self.name}가 삭제되었습니다.')
  
    def setName(self, name):
        self.name = name
        print(f'이름이 {self.name}으로 변경되었습니다.')
        
    def bark(self):
        print('pikachu!!!!!!!!!!!!!')
    
    # 몸통박치기
    def body_attack(self, enemy):
        # 인자값 체크
        if type(enemy) == Pikachu:
            if enemy.hp > 0:
                print(f'몸통박치기로 {enemy.name}의 hp가 {self.level*5} 차감되었습니다.')
                enemy.hp -= self.level*5
                self.check_levelup()
                self.check_enemy_hp(enemy)
            else:
                print('공격할 상대가 없습니다.')
        else:
            print('피카츄를 입력해주세요')
            
    # 십만볼트
    def thousond_volt(self, enemy):
        # 인자값 체크
        if type(enemy) == Pikachu: 
            if enemy.hp > 0:
                print(f'십만볼트로 {enemy.name}의 hp가 {self.level*7} 차감되었습니다.')
                enemy.hp -= self.level*7
                self.check_levelup()
                self.check_enemy_hp(enemy)
            else:
                print('공격할 상대가 없습니다.')
        else:
            print('피카츄를 입력해주세요')    
    
    def check_levelup(self):
        if self.exp >= self.level*100:
            self.exp = self.exp - (self.level*100)
            self.level += 1
            print(f'{self.name}님의 level이 {self.level}이 되었습니다.')  
            
    def check_enemy_hp(self, enemy):
        if enemy.hp <= 0:
            self.exp += enemy.level*15
            enemy.hp = 0
            print(f'상대방을 쓰러뜨려 {self.name}님의 exp가 {enemy.level*15} 증가되었습니다.')
            
    # 포켓몬 정보    
    def info(self):
        print('============')
        print(f'name : {self.name}')
        print(f'level: {self.level}')
        print(f'hp   : {self.hp}')
        print(f'exp  : {self.exp}')
```

```python
p1 = Pikachu('ssafy')
p2 = Pikachu('enemy')
p1.body_attack(p2)
p1.info()
p2.info()
p1.thousond_volt(p2)
p1.bark()
```

```
피카츄 ssafy가 생성되었습니다.
피카츄 ssafy가 삭제되었습니다.
몸통박치기로 enemy의 hp가 25 차감되었습니다.
============
name : ssafy
level: 5
hp   : 100
exp  : 0
============
name : enemy
level: 5
hp   : 75
exp  : 0
십만볼트로 enemy의 hp가 35 차감되었습니다.
============
name : ssafy
level: 5
hp   : 100
exp  : 0
============
name : enemy
level: 5
hp   : 40
exp  : 0
```

