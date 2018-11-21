# 실전 DApp 강의 - note
블록체인에 기록하는 Note DApp

## Contract Deployment (Contract 배포)
  * Contract source -> Solidity Compiler -> ABI |  -> Contract Bytecode -> Ropsten Network

  * Remix : 코드 작성 및 배포 가능, but test가 어려움

  * Testing을 위한 로컬 환경설정 필요

### 1. Build project

```shell
> cd ~
> mkdir dapp
> mkdir note
> cd note

note> pwd
C:₩Users₩[Username]₩dapp₩note

note> mkdir contracts
note> npm init

# Enter 여러번.

note> ls
package.json
```

### 2. Install `solc`
`solc` 솔리디티 컴파일러 설치

```shell
note> npm install --save solc@0.4.24
```

### 3. `note\contracts\` 에 `Note.sol` 을 생성후 편집

```solidity
pragma solidity ^0.4.24;

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
- `ABI`(Application binary interface) : Contract에 구현된 함수들에 대한 interface
- `bytecode` : EVM에서 구동될 compile된 bytecode

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
`web3`와 `ganache-cli` 설치
- `web3`는 Ethereum Network 와 Interact할 수 있게 하는 JS library.
- `ganache-cli` 는 로컬 Ehtereum network를 구축(로컬 테스트를 위한 환경, ganache의 Command Line Interface)
```shell
note> npm install --save web3 ganache-cli
```

#### (주의)에러 발생 !
- 멀티캠퍼스 방화벽 환경으로 인해 github에서 내려 받는 소스를 다운로드 받을 수 없어 생기는 문제
- console창에 다음의 코드를 입력하세요
```shell
> git config --global url."https://".insteadOf git://
```

* 이제 Test를 통해 각 개발 단계(functional, compilation, deployment)에서 코드가 정상적으로 작동하는지 테스트해야 합니다.
* [실전 DApp 첫번째(2) : Test 붙이기](DApp_7_test.md) 를 참고해주세요.
