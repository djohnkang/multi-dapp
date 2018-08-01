# Mocha를 통한 JS testing & TDD
Mocha test framework를 활용한 TDD

### Mocha 기초 functions

| Function     | Purpose                                       |
| ------------ | --------------------------------------------- |
| `it`         | Test 를 실행하고, 주장/가정(Assertion)을 생성 |
| `describe`   | `it` function 을 Group 화 한다.               |
| `beforeEach` | 일반적인 setup 코드를 실행한다.               |

* Test Code Examples

```js
const assert = require('assert');

class Car {
    park() {
        return 'stopped';
    }

    drive() {
        return 'vroom';
    }
}

let car;

beforeEach(() => {
    car = new Car();
});

decribe('fancyCar', () => {
    it('should park', () => {
        assert.equal(car.park(), 'stopped');
    });

    it('should drive', () => {
        assert.equal(car.drive(), 'vroom');
    });
});
```

### 1. `mocha` 설치 및 test 코드 파일 작성

1. `note\package.json` 에서 `"scripts": { "test" : "mocha" }` 로 변경

2. `note\test` 폴더 생성 => `note\test\note.test.js` 파일 생성

3. `note\test\note.test.js`

``` js
const assert = require('assert');
const ganache = require('ganache-cli');

// Constructor function 을 사용할 예정이기 때문에 'W'eb3
const Web3 = require('web3');

// instance 이기 때문에 'w'eb3
const web3 = new Web3(ganache.provider());
```

### 2. `note\test\note.test.js`

```js
const assert = require('assert');
const ganache = require('ganache-cli');

// Constructor function 을 사용할 예정이기 때문에 'W'eb3
const Web3 = require('web3');
// instance 이기 때문에 'w'eb3
const web3 = new Web3(ganache.provider());

beforeEach(() => {
    //  unlock 상태의 계정들을 생성
    web3.eth.getAccouts()
        .then( fetchedAccounts => {
        console.log(fetchedAccounts);
    });
});

describe('Note', () => {
    it('deploys a contract', () => {});
});
```

* 위 코드는 accounts 를 보여줌.
* 이어서 생성된 accounts 중 하나를 사용하여 실제로 contract 를 배포.

### 3. `note\test\note.test.js`

```js
const assert = require('assert');
const ganache = require('ganache-cli');

// Constructor function 을 사용할 예정이기 때문에 'W'eb3
const Web3 = require('web3');
// instance 이기 때문에 'web3'
const web3 = new Web3(ganache.provider());
const { interface, bytecode } = require('../compile');

let accounts;
let note;

beforeEach(async () => {
    accounts = await web3.eth.getAccounts();

    note = await new web3.eth.Contract(JSON.parse(interface))        // web3 lib 에게 현재 우리가 생성할 contract 가 어떤 methods 를 가지고 있는지 설명
        .deploy({ data: bytecode, arguments: ['Hack Your Life'] })   // web3 에게 args 로 init 한 contract 를 배포하겠다고 명시
        .send({ from: accounts[0], gas: '1000000' })                 // web3 가 Ethereum 네트워크로 sender 와 gas 를 명시하여 contract 를 전송.
});

describe('Note', () => {
    it('deploys a contract', () => {
        console.log(note);
    });
});
```

### 4. `note\test\note.test.js`

```js
const assert = require('assert');
const ganache = require('ganache-cli');

// Constructor function 을 사용할 예정이기 때문에 'W'eb3
const Web3 = require('web3');
// instance 이기 때문에 'w'eb3
const web3 = new Web3(ganache.provider());
const { interface, bytecode } = require('../compile');

let accounts;
let note;

beforeEach(async () => {
    accounts = await web3.eth.getAccounts();

    note = await new web3.eth.Contract(JSON.parse(interface))     
        .deploy({ data: bytecode, arguments: ['Hack Your Life'] })
        .send({ from: accounts[0], gas: '1000000' })                
});

describe('Note', () => {
    it('deploys a contract', () => {
        assert.ok(note.options.address);
    });

    it('has a default note', async () => {
        // note() 와 call() 에 주의
        const note = await note.methods.note().call();
        assert.equal(note, 'Hack Your Life')
    });

    it('can change the note', async () => {
        await note.methods.writeNote('bye').send({ from: accounts[0] });
        const note = await note.methods.note().call();
        assert.equal(note, 'bye');
    });
});
```

* Test 가 성공적을 끝나면 실제 배포를 해볼 차례다.
* `note\deploy.js` 를 생성하고 배포코드를 작성해보자.