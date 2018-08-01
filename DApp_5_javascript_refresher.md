# ES6 - Javascript Refresher
ES6(Javascript) refresher & reference

## I. 기초 문법
### 1. Variable & Data type
1. 변수와 상수
```js
const name = "john";    // 상수 선언
let age = 34;   // 변수 선언
var isMale = true;  
```

## II. Array & Object
### 1. Array와 methods
```js
```

### 2. Ojbect와 methods
```js
```

## III. Function
### 1. Function 기초
```js
function hello() {

}

const hello = function() {

} 
```

### 2. Arrow Function(ES6)
```js
const hello = () => {
    
}
```

## IV. OOP
### 1. `class`를 활용한 클래스 만들기 
```js
class Car {
    park() {
        return 'stopped';
    }

    drive() {
        return 'vroom';
    }
}

let car = new Car();
```

## V. Library 및 File 활용 및 조작
### 1. 외부 Library 불러오기
```js
const fs = require('fs'); // file system 이라는 library 불러오기
const data = fs.readFileSync('/file.md'); // file 읽기
```

### 2. File 조작

## VI. Async 비동기 함수와 처리
### 1. 일반적인 오해 : javscript는 비동기?
동기(synchronous, blocking) 함수 
```js
sleep()
```

### 2. 비동기 function
```js
setTimeOut()
```

### 3. `Promise` 객체

### 4. `async` & `await`