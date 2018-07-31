# 실전 DApp 강의 - note
블록체인에 기록하는 Note DApp

## Contract Deployment (Contract 배포)
  * Contract source -> Solidity Compiler -> ABI |  -> Contract Bytecode -> Rinkeby

  * Remix : 코드 작성 및 배포 가능, but test가 어려움

  * Testing을 위한 로컬 환경설정 필요


### 1. Build project

```shell
> cd ~
> mkdir dApp
> mkdir note
> cd note

note> pwd
C:₩Users₩[Username]₩dApp₩note

note> mkdir contracts
note> npm init

Enter 여러번.

note> ls
package.json
```

### 2. Install npm modules

```powershell
note> npm install --save solc
```

### 3. `note\contracts\` 에 `Note.sol` 을 생성후 편집

```solidity
pragma solidity ^0.4.21;

contract Note {

  string public note;

  constructor(string _input) public {
    note = _input;    
  }

  function writeNote(string _input) public {
    note = _input;
  }    
}
```

### 4. `note\compile.js` 생성 - edit

```javascript
const path = require('path');
const fs = require('fs');
const solc = require('solc');

const notePath = path.resolve(__dirname, 'contracts', 'note.sol');
const source = fs.readFileSync(notePath, 'utf8');

console.log(solc.compile(source, 1));

```

### 5. `node compile.js` => Structure 확인.

### 6. 코드 수정

```js
const path = require('path');
const fs = require('fs');
const solc = require('solc');

const notePath = path.resolve(__dirname, 'contracts', 'Note.sol');
const source = fs.readFileSync(notePath, 'utf8');

// contracts object 에서 key ':Note'의 bytecode 만 추출
module.exports = solc.compile(source, 1).contracts[':Note'];
```

### 7. npm module 추가 설치

```shell
note> npm install --save mocha ganache-cli web3
```

* `web3`는 Ethereum Network 와 JS app 의 중간 포털.
* `ganache` 는 로컬 Ehtereum network를 구축.
* `mocha` 는 테스트 프레임워크.

### 8. `note\package.json` 에서 `"scripts": { "test" : "mocha" }` 로 변경

### 9. `note\test` 폴더 생성 => `note\test\note.test.js` 파일 생성

### 10. `note\test\note.test.js`

``` js
const assert = require('assert');
const ganache = require('ganache-cli');

// Constructor function 을 사용할 예정이기 때문에 'W'eb3
const Web3 = require('web3');

// instance 이기 때문에 'w'eb3
const web3 = new Web3(ganache.provider());
```

### 11. Mocha 기초 functions

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
        return 'vroom'
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

### 12. `note\test\note.test.js`

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

### 13. `note\test\note.test.js`

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

### 14. `note\test\note.test.js`

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

### 15. https://infura.io 가입 및 네트워크 보기.

### 16. `truffle-hdwallet-provider` 설치

```powershell
note> npm install --save truffle-hdwallet-provider
```

### 17. `note\deploy.js`

```js
const HDWalletProvider = require('truffle-hdwallet-provider');
const Web3 = require('web3');
const { interface, bytecode } = require('./compile');

const provider = new HDWalletProvider(
    '12 mnemonic words',
    'Infura 에서 제공한 Ropsten 주소'
);

const web3 = new Web3(provider);

const deploy = async () => {
    const accounts = await web3.eth.getAccounts();

    console.log('Attempting to deploy from account', accounts[0]);

    const result = await new web3.eth.Contract(JSON.parse(interface))
        .deploy({ data: '0x' + bytecode, arguments: ['Hi there!'] })
        .send({ from: accounts[0], gas: '1000000', gasPrice: web3.utils.toWei('2', 'gwei') });

  console.log('Contract deployed to', result.options.address);  
};

deploy();
```

```shell
Attempting to deploy from account [acoount_address]
Contract deployed to [contrcat_address]
```

* Console 에 Contract Address 가 나타난다면, https://rinkeby.etherscan.io/address/[contract_address] 를 입력하고 실제 배포되었는지 확인해보자.

## Authors
Translated, edited, and written by
- 강동주 [djohnkang](https://github.com/djohnkang)
- 유태영 [neovansoarer](https://github.com/neovansoarer)
- 오창희 [5chang2](https://github.com/5chang2)

## Acknowledgement
- Stephen Grider [StephenGrider](https://github.com/StephenGrider)
  - [ethereum and solidity the complete developer's guide](https://www.udemy.com/ethereum-and-solidity-the-complete-developers-guide/learn/v4/t/lecture/9025578?start=1)

## License
Under the [MIT License](https://opensource.org/licenses/MIT).
