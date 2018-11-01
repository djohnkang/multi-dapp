# 실전 dApp 강의 - Token 발행
외부 Library 활용한 Token 발행

## ERC20-compliant 토큰 발행

- Zeppelin Solutions : a smart contract auditing service
- 보안 이슈로 인해 token 발행의 경우 이미 검증된 라이브러리를 활용하는 것이 좋음
- `zeppelin-solidity`를 활용하여 토큰 발행

## 목차
1. 프론트엔드 어플리케이션 `unbox`
2. `TutorialToken` 스마트 컨트랙 작성
3. Compile & Deploy with `truffle`
4. 발행한 토큰 사용하기

### 1. 프론트엔드 어플리케이션 unbox
#### truffle 설치
- https://truffleframework.com
- Solidity 컨트랙 프로그래밍을 위한 개발 프레임워크 
```shell
npm install -g truffle 
```

#### 프로젝트 셋업
```
mkdir mytoken
cd mytoken

truffle unbox tutorialtoken

npm install --save zeppelin-solidity
```

### 2. TutorialToken 스마트 컨트랙 작성
#### 컨트랙 작성
- `TutorialToken.sol` 작성
```
pragma solidity ^0.4.24;

// 경고 뜨지만 괜찮음.
import 'zeppelin-solidity/contracts/token/ERC20/MintableToken.sol';

contract TutorialToken is StandardToken {

  string public name = //"나의 코인 이름";
  string public symbol = //"약어";
  uint8 public decimals = 2;
  uint public INITIAL_SUPPLY = 12000;

  constructor() public {
    totalSupply_ = INITIAL_SUPPLY;
    balances[msg.sender] = INITIAL_SUPPLY;
  }
}
```

### 3. `truffle`로 Compile & Deploy

#### (1) Compile

- `migrations/2_deploy_contracts.js` 파일 생성

```
var TutorialToken = artifacts.require("TutorialToken");

module.exports = function(deployer) {
  deployer.deploy(TutorialToken);
};
```

- `ganache` 실행 : port 7545에서 로컬 실행

- 컴파일
```
truffle compile
```

#### (2) Deploy
- `migrate` == `deploy`
```
truffle migrate
```

- 메타마스크로 접속
  0. 새로운 브라우저 다운로드(파이어폭스/오페라/브레이브)
  1. 메타마스크 다운로드
  2. **ganache의 mnemonic phrase로 로그인**
  3. Custom RPC
  4. "New RPC URL" => `http://127.0.0.1:7545`
  5. Private Network 확인

![metamask](http://truffleframework.com/tutorials/images/pet-shop/metamask-customrpc.png)


### 4. 발행한 토큰 사용하기

#### (1) 프론트엔드 앱 구동시키기
```
npm run dev
```

#### (2) 토큰 주고 받기
- 첫번째 계정에 토큰 balance 확인
- 두번째 계정에 보내기
- Ganache에서 Transaction 확인
- 두번째 계정에서 확인

## Authors
Translated, edited, and written by
- 강동주 [djohnkang](https://github.com/djohnkang)

## Acknowledgement
- [Truffle Tutorial + Open Zeppelin](http://truffleframework.com/tutorials/robust-smart-contracts-with-openzeppelin)

## License
Under the [MIT License](https://opensource.org/licenses/MIT).
