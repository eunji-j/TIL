## 191029_Web_JavaScript

<br>

javascript (ES6버전)



DOM  문서 조작(많이 하게 될것)

ex. window.document.title = 'javascript'

BOM 브라우저 조작

>ex. window.print(), window.open('https://naver.com')

- 프론트, 백엔드 모두 가능.

- 기본적인 연산도 가능하다.

- 컴퓨터(프로그램) 조작을 할 수 없다.(반쪽짜리언어) -> node.js 가 나오게 됨. -> javascript 수요가 폭발적으로 증가. 



Chrome -> `F12` -> Console에서 조작이 가능함.

### Dom 조작

> intro > main.js

```js
alert('Hello world!!!')

// 여기는 주석입니다.
/*
여기서부터
여기까지는
주석입니다.
*/


document.write('<h1>hello world</h1>')

// h1태그를 가져와!
document.querySelector('h1')

// 가져온 태그의 내부 text를 바꿔줘
document.querySelector('h1').innerText = "Bye"

// 선언한 변수를 콘솔창에서 조작가능하다.
var name = "eunji"

// print()와 동일
console.log(name)


// var타입(-> 잘 이용하지않을 것)
var b = 30
// var 특성상 b가 0으로 바뀌게 된다.
for (var b = 0; b < 10; b++) {
        console.log(b)
        // 0 1 2 ... 10
    }
console.log('!!!!!!!!!!!!!!!')
console.log(b)


// let타입(재할당 가능)
let name = 'eunji'
document.write(name)

name = 'eunji-j'
document.write(name)


// const타입(상수로 사용됨. 재할당 불가능 -> 주로 이용)
const loca = 'GJ'
document.write(loca)
// loca = 'seoul'
// document.write(loca)


// 문자열 합치기
const first_name = 'eunji'
const last_name = 'jeong'

const full_name = first_name + last_name

document.write('<h1>'+full_name+'</h1>')
document.write(`<h1>${full_name}</h1>`)
console.log(`<h1>${full_name}</h1>`)


// 데이터 입력받기
const userName = prompt("hello who are you???")
let message = ``
document.write(message)


// if문
if (userName === 'eunji') {
    message = `<h1>admin page</h1>`
}else if (userName === 'happy') {
    message = `<h1>happy coding</h1>`
}else {
    message = `<h1>hello! ${userName}</h1>`
}
document.write(message)


// 타입비교
const num1 = 0
const num2 = "0"

// 느슨한 같음(값을 비교)
console.log(num1 == num2)
// 엄격한 같음(타입까지 비교)
console.log(num1 === num2)
```

<br>

### node js

> https://nodejs.org/ko/ -> 12.13.0 LTS 다운로드
>
> bash에서 javascript 사용해보자

```bash
$ node 파일명
```

- VisualStudioCode 확장프로그램 **ESLint, Beautify** 설치



### 0. 변수

> 00_variable.js

```js
// let타입은 같은 이름의 변수를 한번만 선언가능하다.(할당은 여러번가능)
let x = 1
// let x = 2 불가능
x = 3 // 가능
console.log(x)


// let타입은 Block Scope 블록 유효범위
let x = 1

if (x === 1) {
    let x = 2
    console.log(x) // 2
}
console.log(x) // 1


// let은 초기화 안해도 가능, const는 초기화 해야한다.
let x
const y = 9
// y = 10 불가능

if (y === 9) {
    let y = 20
    console.log(y) // 20
}
console.log(y) // 9


// def varTest(): 동일
function  varTest() {
    var x = 1
    if (true) {
        var x = 2
        console.log(x) // 2
    }
    console.log(x) //2
}
varTest()
console.log(x) // 오류


// var : 선언, 할당 => 자유 / 함수 스코프
// let : 할당 => 자유 / 선언 => 한번만 / 블록 스코프
// const : 할당, 선언 => 한번만 / 블록 스코프


let dog
let variableName

let dogs = []

function getName() {
}

// event
const onClick = () => {}

let isValid = false

// class(대문자시작)
class User {
    constructor(value) {
        this.name = value.name
    }
}

// 완전한 상수
const API_KEY = "asdff:123456123"

const a = 13
const b = -5
const c = 3.14
const d = 2.9e8
const e = Infinity // 양수 무한대
const f = -Infinity
const g = NaN // Not a Number의미
console.log(typeof e)
console.log(Math.sqrt(-2)) // NaN

const sentence1 = 'hello\n'
const sentence2 = "hello"
const sentence3 = `helloworld${sentence2}`
console.log(sentence3+sentence1)

// true, false 소문자
const isValid = true // false

// null / undefined
let first_name
console.log(first_name) // undefined(할당이 되지 않을 때)
console.log(typeof first_name) //undefined

let last_name = null
console.log(last_name) // null(빈값)
console.log(typeof last_name) // object

console.log(null == undefined) // true
console.log(null === undefined) // false
console.log(null + 1) // 1
console.log(undefined + 1) // NaN
```

<br>

### 1. 연산자

> 01_operator.js

```js
// let a = 0

// + - * /
a = a + 3
a += 3
a++ 
console.log(a) // 7


console.log(3<2) // false
console.log('a' < 'b') // true
console.log */('가'<'나') // true
 
const a = 1
const b = '1'
console.log(a == b) // true
console.log(a === b) // false
console.log(a === Number(b)) // true

// and
console.log(true && false) // false
// or
console.log(true || false) // true
// not 
console.log(!true) // false

// 삼항연산자
const a = 10
const result = a > 5 ? "5이상" : "5미만"
console.log(result)
```

<br>

### 2. 제어문

> 02_control_of_flow.js

```js
// node에서 사용자 입력받는 방법(복잡하다.)
// const readline = require('readline')
// const userName = readline.createInterface(
//     {
//         input: process.stdin,
//         output: process.stdout
//     }
// )

// userName.question('이름을 입력하세요', (answer) => {
//     console.log(answer)
//     userName.close()
// })


// if문
const userName = prompt('who are you?')
let message = ''

if (userName === 'eunji') {
    message = 'hi admin'
} else if (userName === 'happy') {
    message = 'happy coding'
} else {
    message = `hello ${userName}`
}
console.log(message)


// switch문
const userName = prompt('who are you?')
let message = ''

switch(userName) {
    case 'admin': {
        message = 'hi admin'
        break
    }
    case 'eunji': {
        message = 'welcome'
        break
    }
    // 모든 case문 다 돌고 난후 실행된다.
    default: {
        message = `hi ${userName}`
    }
}
console.log(message)


// while문
let i = 0

while (i < 5) {
    console.log(i)
    i++
}


// for (초기화 ; 값 비교 ; 값증가) {}
for (let a = 0; a < 5; a++) {
    console.log(a)
}


// 배열 반복문
const numbers = [0, 1, 2, 3, 4, 5]
for (let num of numbers) {
    console.log(num)
}
```

### 3. 함수

> 03_function.js

```js
// 1. 함수 선언식
function add(num1, num2) {
    return num1 + num2
}
console.log(add(2, 10))

// 2. 함수 표현식
const sub = function(num1, num2) {
    return num1 - num2
}
console.log(sub(10, 2))

// 함수 표현식
const ssafy1 = function(name) {
    console.log(`hello ${name}`)
}
ssafy1('eunji')

// 3. 화살표 함수, arrow function
const ssafy2 = (name) => {
    console.log(`hello ${name}`)
}
ssafy2('eunji-j')

// 화살표 함수 소괄호 생략, 단 매개변수가 하나일 때
const ssafy3 = name => {
    console.log(`hello ${name}`)
}
ssafy3('eunji')

// 화살표 함수 중괄호 생략, 표현식 하나일 때
const ssafy4 = name => `hello ${name}`
console.log(ssafy4('eunji-j'))


// 예제
let square = function(num) {
    return num ** 2
}
square = (num) => {
    return num ** 2
}
square = num => {
    return num ** 2
}
square = num => num ** 2


let noArgs = () => 'no args'
noArgs = _ => 'no args'
console.log(noArgs())


// 딕셔너리
const a = {}
console.log(typeof a) // object(json형태)

let returnObject = () => ({key: 'value'})

// 기본값 설정
const hello = (name="noName") => `hello ${name}`
console.log(hello('change'))


// 기명함수
const hello = function (name) {
    console.log(name)
}
hello('change')

// 익명함수(즉시실행함수)
(function (name) {
    console.log(name)
})('change')
```

<br>

### 4. 배열

> 04_array.js    [array 참고](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array)

```js
const numbers = [1,2,3,4]

console.log(numbers[0]) // 1
console.log(numbers.length) // 4

console.log(numbers.reverse()) // 원본을 바꿔줌
console.log(numbers)

// 맨마지막요소에 접근
numbers.push(5)
console.log(numbers)

numbers.pop()
console.log(numbers)

// 맨처음요소에 접근
numbers.unshift(0)
console.log(numbers)

numbers.shift()
console.log(numbers)


console.log(numbers.includes(100)) // false
console.log(numbers.indexOf(100)) // 1(없으면 -1)
console.log(numbers.join()) // 4,3,2,1
console.log(numbers) // [ 4, 3, 2, 1 ]
```

<br>

### 6. object 생성

> 05_object.js

```js
const me = {
    name: 'change',
    'phone number': '123123123', // 띄어쓰기가 있으면 ''로 묶어준다.
    product: {
        phone: 'iphone',
        notebook: 'mac',
    }
}
console.log(me['name']) // change
console.log(me.product.notebook) // mac


let books = ['javascript', 'python']
let comics = {
    DC: ['Aquaman', 'SuperMan'],
    Marvle: ['Avengers', 'IronMan'],
}

let bookStore = {
    books,
    comics
}
console.log(bookStore)

// JSON
console.log(me)
const jsonData = JSON.stringify(me)
console.log(jsonData)

const parseData = JSON.parse(jsonData)
console.log(parseData)
```