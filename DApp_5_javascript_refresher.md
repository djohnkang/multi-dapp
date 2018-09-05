# ES6 - Javascript Refresher
ES6(Javascript) refresher & reference

## I. 기초 문법
### 1. Data types
```js
console.log(typeof 1); // → number
console.log(typeof 4.5); // → number
console.log(typeof 'x'); // → string

console.log(1 > 3); // → false
console.log(5 > 3); // → true

console.log(true || false); // → true
console.log(false && true); // → false
```

### 2. 변수와 상수

```js
let name = 'Dapp';
name = 'Decentralized Application';

const lecturer = 'John';
// lecturer = 'Neo'; (Unable)
```

## II. Array & Object
### 1. Array와 methods
```js
let numbers = [2, 3, 5, 7, 11];
console.log(numbers[0]);
// → 2
console.log(numbers[4]);
// → 11

let sequence = [1, 2, 3];
sequence.push(4);
sequence.push(5);
console.log(sequence);
// → [1, 2, 3, 4, 5]
console.log(sequence.pop());
// → 5
console.log(sequence);
// → [1, 2, 3, 4]
console.log(sequence.length);
// → 4
```

### 2. Ojbect와 methods
```js
let classInfo = {
  name: 'Dapp',
  location: 'Multicampus',
  curriculum: [
    'Ethereum & Dapp basic + Solidity + Remix',
    'Local env setting',
    'Geth & Mist',
    'Deployment env setting',
    'Javascript Refresher',
    'Dapp1: Note, Test, Deployment',
    'Dapp2: Lottery',
    'Dapp3: Crowd fund',
    'Dapp4: My ERC-20 Token'
  ],
  lecturer: {
    main: 'John',
    assistant: ['Neo', 'Chang2']
  },
};

classInfo.name = 'Decentralized Application';

console.log(classInfo);
```

## III. Function
### 1. Function 기초
```js
const power = function (base, exponent) {
  let result = 1;
  for (let count = 0; count < exponent; count++) {
    result *= base;
  }
  return result;
};
console.log(power(2, 10)); // → 1024
```

```js
function power(base, exponent) {
    let result = 1;
    for (let count = 0; count < exponent; count++) {
      result *= base;
    }
    return result;
};
console.log(power(3, 3)); // → 27
```

### 2. Arrow Function(ES6)

```js
const square1 = function (x) { return x * x; };

const square2 = (x) => { return x * x; };

const square3 = x => x * x;
```

## IV. OOP
### 1. `class`를 활용한 클래스 만들기 
```js
class Car {
  constructor(brand, model) {
    this.brand = brand;
    this.model = model;
  }

  park () {
    return 'Car Stopped';
  }

  drive () {
    return 'Vrooooom';
  }
}

let car = new Car('Mercedes', 'S500');

console.log(car.brand + '-' + car.model); // → Mercedes S500
console.log(car.park()); // → Car Stopped
console.log(car.drive()); // → Vrooooom
```

## V. Library 및 File 활용 및 조작
### 1. 외부 Library 불러오기 & File 조작
```js
const fs = require('fs'); // include <File System> Module

// Create File
try {
  fs.appendFileSync('test.txt', 'HappyHacking\n');
  console.log('---File created!---');
}catch (e) {
  console.log(e);
}

// Append data
let tetxToAdd = 'Ethereum based Dapp';
try {
  fs.appendFileSync('./test.txt', tetxToAdd, 'utf-8');
  console.log('---Write Done!---');
}catch(e){
  console.log(e);
}

// Read File
const data = fs.readFileSync('./test.txt', 'utf-8');

console.log('---content of text file---')
console.log(data);
```



## VI. Async 비동기 함수와 처리

### 1. 비동기 function
```js
const asyncMsg = () => {
  console.log('하나!');

  setTimeout( () => {
      console.log('.....')
    }, 3000);

  console.log('둘!');
};

asyncMsg();
```

### 2. `Promise` 객체와 `async` & `await`

```js
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function syncMsg() {
  console.log('하나!');
  console.log('.....');
  await sleep(3000);
  console.log('둘!')
}

syncMsg();
```