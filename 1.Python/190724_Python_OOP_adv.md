## 190724_Python_OOP_adv

<br>

### 01.주요 학습내용

#### 1. 클래스 변수 / 인스턴스 변수

- 클래스의 속성입니다.

```python
class TestClass:
      class_variable = '클래스변수'
      ...

TestClass.class_variable  
# '클래스변수'
TestClass.class_variable = 'class variable'
TestClass.class_variable  
# 'class variable'
```

- 인스턴스의 속성입니다.

```python
class TestClass:
	def __init__(self, arg1, arg2):
		self.instance_var1 = arg1 # 인스턴스 변수
		self.instance_var2 = arg2

	def status(self):
		return self.instance_var1, self.instance_var2   

tc = TestClass(1, 2)
tc.instance_var1  
# 1
tc.instance_var2  
# 2
tc.status()  
# (1, 2)
```



<br>

#### 2. 인스턴스 메서드 / 클래스 메서드 / 스태틱(정적) 메서드

```python
class MyClass:
    def instance_method(self): # 인스턴스 메서드
        return self
    
    @classmethod
    def class_method(cls):	# 클래스 메서드
        return cls
    
    @staticmethod
    def static_method(arg):	 # 스태틱 메서드
        return arg

mc = MyClass()
```

```python
# 인스턴스 입장에서 확인해 봅시다.
print(mc.instance_method())
mc.instance_method() == mc # True
print(mc.class_method()) # 가능하지만 사용하지 않아야 함
mc.class_method() == MyClass # True
print(mc.static_method(123)) # 가능하지만 사용하지 않아야 함
```

```
<__main__.MyClass object at 0x000001C804779400>
<class '__main__.MyClass'>
123
```

#### 정리

- 클래스에서 인스턴스메서드는 **접근할 수 있지만 호출하지 않습니다.** (가능하다 != 사용한다)
- 클래스가 할 행동은 다음 원칙에 따라 설계합니다.
  - 클래스 자체(`cls`)와 그 속성에 접근할 필요가 있다면 클래스메서드로 정의합니다.
  - 클래스와 클래스 속성에 접근할 필요가 없다면 스태틱메서드로 정의합니다.

<br>

#### 3. 상속

- 클래스에서 가장 큰 특징은 '상속' 기능을 가지고 있다는 것이다.
- 부모 클래스의 모든 속성이 자식 클래스에게 상속 되므로 코드재사용성이 높아집니다.

```python
class DerivedClassName(BaseClassName):
    code block
```

- 진짜 상속관계인지 확인해봅시다.

```python
issubclass(자식클래스, 부모클래스)
```

<br>

#### 4. 메서드 오버라이딩

- 메서드를 재정의할 수도 있습니다. (덮어씌우기)

```python
# 부모클래스
class Person:
    
    brain = True
    
    def __init__(self, name, age, number, email):
        self.name = name
        self.age = age
        self.number = number
        self.email = email 
        
    def greeting(self):
        print(f'안녕, {self.name}')
        
    def walk(self):
        print('뚜벅뚜벅')
        
class Student(Person):
    def __init__(self, name, age, number, email, student_id):
        super().__init__(name, age, number, email)
        self.student_id = student_id
        
p1 = Person('홍길동', 200, '0101231234', 'hong@gildong')
s1 = Student('학생', 20, '12312312', 'student@naver.com', '190000')

p1.greeting()
s1.greeting()
```

```
안녕, 홍길동
안녕, 학생
```

```python
# 자식클래스
class Soldier(Person):
    def __init__(self, name, age, number, email, army_id):
        super().__init__(name, age, number, email)
        self.army_id = army_id
    
    def greeting(self):
        print(f'충성! 이병 {self.name}')
        
    def walk(self):
        print('성큼성큼')
        
s1 = Soldier('굳건이', 20, 123123, 'email@email.com', 123456)
s1.greeting()
s1.walk()
s1.brain
```

```
충성! 이병 굳건이
성큼성큼
True
```

#### 상속관계에서의 이름공간

- 인스턴스 -> 자식 클래스 -> 부모 클래스 -> 전역

#### 다중 상속

- 두개 이상의 클래스를 상속받는 경우, 다중상속이 됩니다.

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

> Pikachu 클래스를 상속받는  Raichu 클래스


```python
class Raichu(Pikachu):
    level = 10
    hp = level * 30
    exp = 0
    
    # 생성자
    def __init__(self, name):
        self.name = name
        print(f'라이츄 {self.name}로 진화했습니다.')
    # 소멸자
    def __del__(self):
        print(f'라이츄 {self.name}가 삭제되었습니다.')
            
    # 백만볼트
    def million_volt(self, enemy):
        # 인자값 체크
        if issubclass(type(enemy), Pikachu): 
            if enemy.hp > 0:
                print(f'백만볼트로 {enemy.name}의 hp가 {self.level*30} 차감되었습니다.')
                enemy.hp -= self.level*30
                self.check_levelup()
                self.check_enemy_hp(enemy)
            else:
                print('공격할 상대가 없습니다.')
        else:
            print('라이츄를 입력해주세요')    
```
