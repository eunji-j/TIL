# 200104_Spring_intro

### 프로젝트 생성

- **메이븐 프로젝트** 생성 -> Group Id, Artifact Id 입력

파일

> `pom.xml` : 스프링프레임워크에서 필요한 모듈을 가져오는 곳. (ex. dependencies, build plugins...)

폴더

> `src/main/java` : 패키지명 > 자바코딩파일위치하는 곳. (ex. 클래스, DAO, Service (.java)...)
>
> `src/main/resources` : 리소스들이 있는 곳. (ex. 컨테이너(.xml)...)

<br>

## IOC (스프링 컨테이너) ★

- xml파일을 통해 **스프링 컨테이너**의 **객체(bean)**를 생성한다. 
- new로 생성하지 않아도 **알아서 생성**해준다.(스프링 컨테이너에 메모리 로딩된다.)



#### 1. 컨테이너 생성

> src/main/resources > `applicationContext.xml(리소스명은 달라도 됌)` (스프링 설정파일)

```xml
<beans>
	<bean id="id명" class="패키지명.클래스명"/>
</beans>
```

#### 2. 컨테이너에 접근

```java
// 컨테이너 생성
GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpath:applicationContext.xml(리소스명)");

// 컨테이너에서 객체 가져옴
클래스명 객체명 = ctx.getBean("id명", 클래스명.class);

// 객체의 메소드 사용
객체명.메소드명();

// 컨테이너 반환(리소스낭비 줄임)
ctx.close(); 
```

<br>

## DI (Dependency injection) - 의존성 주입 ★

![1578074627205](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578074627205.png)

**가장 효율적** 방식: 생성자와 Setter메소드에서 둘다 주입(유연성)

가장 비효율적 방식: 생성자에서만 주입

- 스프링설정파일(컨테이너) 사용O / 사용 X

![1578075543152](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578075543152.png) 

<br>

#### 1. 다양한 의존주입방법

- 생성자: constructor-arg 태그

![1578076406529](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578076406529.png)
- Setter메소드: property 태그

![1578076588643](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578076588643.png)
- List 타입: value 태그

![1578076748580](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578076748580.png)
- Map 타입: key, value 태그

![1578076816225](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578076816225.png)

<br>

### 스프링 설정파일(컨테이너) 분리

![1578077015245](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578077015245.png)

일반적으로 기능별로 분리한다. 

(DAO와 Service / 데이터베이스 / infomation등록)

![1578077438887](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578077438887.png)

분리한 파일들을 배열을 이용하여 컨테이너를 생성한다. (일반적)

 

또는, 한파일에 임포트하여 사용하는 방법도 있다. (배열로 가져오지 않아도 된다.)

![1578077530750](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578077530750.png)

<br>

### 빈(Bean)의 범위

![1578077813946](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578077813946.png)

<br>

## 의존객체 자동 주입

#### 1. @Autowired

객체의 타입을 보고 자동으로 주입

프로퍼티, 생성자, 메소드 모두 사용가능하다.

단, 프로퍼티, 메소드에서 사용할 때는 **Default 생성자**를 꼭 명시해줘야 한다.(객체를 찾지 못하기 때문)

![1578079233020](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578079233020.png)

Property 혹은 constructor-arg 태그 사용하지 않아도 된다.

![1578079191610](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578079191610.png)

#### 2. @Resource

객체의 이름을 보고 자동으로 주입

생성자에는 사용하지 못한다.(잘사용하지 않는다)



#### 3. 의존객체 선택(다수일때 에러해결)

#### @qualifier("")

![1578082378276](C:\Users\eunji_j\AppData\Roaming\Typora\typora-user-images\1578082378276.png)



#### 4. @Autowired(required = false)

어노테이션이 사용되었지만 빈이 생성안되었을 때 에러 안나게 해준다.
