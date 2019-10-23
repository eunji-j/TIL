# 191022_Django_N:M

<br>

## 새로 배운 것

### 1)  의사(Doctor) : 환자(Patient) => N:M 관계

manytomany  > `models.py` 

```python
from django.db import models


class Doctor(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Patient(models.Model):
    name = models.CharField(max_length=100)
    doctors = models.ManyToManyField(Doctor, through='Reservation', related_name='patients')

    def __str__(self):
        return self.name

class Reservation(models.Model): # n:m 중간모델
    doctor = models.ForeignKey(Doctor, on_delete=models.CASCADE)
    patient = models.ForeignKey(Patient, on_delete=models.CASCADE)

    def __str__(self):
        return f'{self.doctor.name}의사 : {self.patient.name}환자'
```

### def __str__(self):

```python
class Doctor(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name
    # 객체 호출했을 때 이름이 바로 출력된다.
    	# In [1]: d1 = Doctor.objects.create(name='john')
        # In [2]: d1 = Doctor.objects.get(id=1)
        # In [3]: d1
    # 적용전
    	# Out[3]: <Doctor: Doctor object (1)>
    # 적용후
    	# Out[3]: <Doctor: john>
```

### ManyToManyField

```python
class Patient(models.Model):
    name = models.CharField(max_length=100)
    
    doctors = models.ManyToManyField(Doctor, through='Reservation', related_name='patients')
    # 1.through='Reservation'
    	# patient1에서 doctors라는 컬럼이 만들어진다.
    	# patient1.doctors.all() 명령어가 사용가능해진다. (patient1.doctor_set.all()과 같다.)

    # 2.related_name='patients'
    	# 반대로 doctor에서도 patient에 접근할 수 있게 된다.
        
    def __str__(self):
        return self.name
```
