# 실전 DApp 강의 - basic
**Ethereum과 Interaction** (다양한 Ethereum의 Interface)

## Ethereum 이란 무엇인가.

* Ethereum 네트워크는 가치(Ether)를 전송하고, 그 데이터를 저장한다.
* 세상에는 많은 Ethereum 네트워크가 존재한다.
  * **메인 네트워크가 존재**, 동시에 메인 네트워크 이외에도 많은 네트워크가 존재.
  * 강의 진행 중 **직접 네트워크를 만들어 볼 예정**
* Ethereum 네트워크는 한개 혹은 이상의 **Node** 로 이루어져 있다.
* 여기서 Node 는 Ethereum 클라이언트를 실행하는 모든 종류의 컴퓨터를 의미한다.
  * 즉 컴퓨터에 Ethereum 프로그램을 설치하면 Ethereum Node 가 된다.
* Ethereum Node 는 누구나 돌릴 수 있다.
* 모든 Ethereum Node는 완전한 연속된 블록들(**Blockchain**)을 복사해서 저장한다.
* **Blockchain**은 일어난 모든 거래(transaction)들의 레코드를 저장한 **거래 장부 (or 데이터베이스)** 라고 볼 수 있다.

## Ethereum Network 사용하기

### 사용자
  * **`Metamask`** : 브라우저에서 활용할 수 있는 Ethereum Wallet
  * 하나의 계정으로 모든 네트워크 사용
  * 가입 & 12words 저장.
  * Ropsetn Test Network 로 이동.
  * https://faucet.metamask.io/ 에서 ether 받기.

### 개발자
  * **`web3.js`** : Ethereum javacript API
  * Ethereum 네트워크와 Interaction 할 수 있는 Javascript Library
  * 다양한 Javascript runtime 환경에서 활용 가능 : 브라우저, local machine(node.js)    
  * **DApp 활용을 위한 표준 Interface로 자리 잡고 있음**
  * `Metamask`도 `web3.js`를 기반으로 만들어짐
  * https://github.com/ethereum/web3.js/

## Ethereum Transaction
사용자(User)의 입장에서 Ethereum Network 활용하기

### Ether 주고 받기
  1. `Metamask` 설치 :  https://metamask.io/

  2. Ropsten 네트워크 설정

  3. Faucet에서 Ether 받아오기 : https://faucet.metamask.io/

### Ether Faucet(Ropsten)
1. `request 1 ether from faucet` 누름

2. address 가 서버로 제출

3. 서버가 `web3.js` 라이브러리를 사용하여 `transaction` Object를생성

  * Transaction : 한 계정에서 다른 계정으로 돈을 보내는 행위 (Blockchain 의 데이터 변화 그 자체)

  * 송금(money sending) Transaction 의 구성

  | name | Description |
  | ----------------- | ------------------------------------------------------------ |
   | nonce             | 전송자가 몇번 거래를 전송했는가. (number)                    |
   | to                | 돈이 가는 곳의 계정 주소                                     |
   | value             | to 로 전송되는 Ether 의 양                                   |
   | gasPrice          |                                                              ??|
   | startGas/gasLimit | 전송자가 해당 transaction 을 실행하기 위해 지불하고자 하는 최대 gas  양. |
   | v                 | 전송자의 계정주소를 의미하는                                 |
   | r                 | 엄청나게 보안적으로 복잡한 데이터들                          |
   | s                 | 전송자의 private key 를 통해 생성됨. private key => v-r-s  / v-r-s =/=> pk |

4. 서버가 `transaction` Object 를 Ropsten 테스트 네트워크로 전송

5. 서버는 Ropsten 테스트 네트워크가 Transaction 이 완료되었는지 확인할 동안 기다림.

### 왜 시간이 오래 걸리는가?
Ethereum Transaction의 내부 원리
1. `transaction` 이 Ethereum 네트워크로 (특정 Node로) 전송됨.
2. 해당 Node가 우리가 전송한 `transaction` 이외의 다른 `transaction` 들을 모아서  Block 으로 만듬.
3. Node 가 유효성검사(Validation Logic)를 통해 해당 Transaction 들을 인증(confirm) 함.
4. Target 보다 낮은 숫자가 되도록 Nonce 값을 조정함.
5. 찾는다면 Block 이 생성됨.
6. **위 1 ~ 5 까지의 process 가 실행되는데 걸리는 시간이 Blocktime 이며, 일반적으로 15초정도 걸린다. 이유는 Blockchain 네트워크가 평균시간을 15초에 수렴하도록 하기위해 Target 을 통해 난이도를 조절하기 때문.**
7. 앞으로 진행할 Local Network 에서는 시간이 거의 걸리지 않을것. 단순히 편한 개발을 위함.

## Smart Contract
Self-excuting Contract
* 코드에 의해 관리되는 계약.
* Ethereum의 Account는 `Contract Account(컨트랙 계정)`와 `EOA(Externally Owned Account, 일반계정)`로 나뉜다.
* 지금까지 `Metamask`로 만든 Account는 `EOA`
* Smart Contract는 `Contract Account`에 배포 된다.
* `Contract Account` 의 구성

  | Field   | Description                          |
  | ------- | ------------------------------------ |
  | Balance | Account 가 가지고 있는 Ether 양.     |
  | Storage | 해당 거래의 데이터를 저장하는 저장고 |
  | Code    | 해당 거래를 위한 기계어 코드         |

## Solidity & Remix

### First Contract : 직접 Contract를 만들어 봅시다 :)
<https://remix.ethereum.org>로 접속

```solidity
// 사용할 solidity version 명시
pragma solidity ^0.4.21;

// 새로운 Contract 를 정의(Class로 생각하세요)
contract HelloWorld {

    function hello() public pure returns (string) {
        return "hello world!"
    }
}
```

* Contract function 의 실행

  | function 호출                      | function 을 통해 transaction 이 일어남. |
  | ---------------------------------- | --------------------------------------- |
  | Contract 의 data 를 수정할 수 없음 | Contract 의 data 를 수정할 수 있음      |
  | 바로 실행됨                        | 실행에 시간이 걸림                      |
  | Data 를 리턴 가능                  | transaction 의 hash 값을 return         |
  | 무료                               | 유료                                    |

* External Account 가 Contract 를 생성하는 Transaction 의 구성

  | name              | Description                                                  |
  | ----------------- | ------------------------------------------------------------ |
  | nonce             | 해당 계정에서 생성한 transaction 수.                         |
  | to                | - (to 가 비어있으면, Ethereum 네트워크가 자동으로 contract 생성이라고 판단.) |
  | **data**          | 해당 Contract 의 컴파일 된 기계코드                          |
  | value             | 타겟 계정으로 전송될 'Wei' 의 양                             |
  | gasPrice          | 해당 transaction 을 진행할 때 전송자(contract 를 생성하는 계정)가 사용할 gas 1 유닛 의 wei 가격. |
  | startGas/gasLimit | 해당 transaction 이 소비할 수 있는 가스의 유닛 수            |
  | v                 | 전송자의 계정주소를 의미하는                                 |
  | r                 | 엄청나게 보안적으로 복잡한 데이터들                          |
  | s                 | 전송자의 private key 를 통해 생성됨. private key => v-r-s  / v-r-s =/=> pk |

* 블록체인 상의 데이터를 바꾼다 => Submit a transaction (transaction 제출)

### Solidity 기초
Smart Contract를 구현하기 위한 컨트랙트 기반의 **고급 프로그래밍 언어**
- **고급** : Ethereum Bytecode(Turing Complete)를 직접 작성할 수 없음 -> 인간 언어에 가까운 언어로 작성해야함

- **프로그래밍 언어** : 데이터를 **저장** & 데이터를 **계산(조작)**

- 정적타입, 상속, 라이브러리, 복잡한 사용자 정의 자료형을 지원

#### 1. Data의 저장

* 기본 자료형(Basic Data Types)
* 변수(Variable)
* 배열(Array)
* 매핑(Mapping)
* 구조체(Struct)

##### (1) Solidity 의 Basic Data types

| Name         | Descriptions                            | Examples                  |
| ------------ | --------------------------------------- | ------------------------- |
| string       | 연속된 문자들                           | `"Hack"` `"Life"`         |
| bool         | 참/거짓 값                              | `true` `false`            |
| int          | 정수값, 음수/양수, 소숫점 없음          | `0` `-30000` `59158`      |
| uint         | 'Unsigned' int, 양수, 소숫점 없음       | `0` `30000` `999910`      |
| fixed/ufixed | 'Fixed' point number. 소숫점 있음       | `10.1`, `-42.2` `3.14`    |
| address      | Money 를 전송하는 메서드가 포함되어있음 | `0x134bae199c8dbae188c8d` |

##### (2) 변수
단일 데이터 저장

```solidity
// 사용할 solidity version 명시
pragma solidity ^0.4.21;

// 새로운 Contract 를 정의(Class로 생각하세요)
contract Me {

  string name;
  uint age;
  bool isMarried;

  function getName() public view returns (string) {
      return "hello world!"
  }
}
```

##### (3) 배열
연속된 데이터 저장

```solidity
pragma solidity ^0.4.21;

// 강의(Lecture) Contract 생성
contract Lecture {

  string lecturer; // 강의자
  string[] students; // 학생

  // function getLecturer() -> 강의자를 불러오는 함수
  // function getStudent() -> 특정 학생을 불러오는 함수
  // function getNumberOfStudents() -> 학생 전체의 수를 불러오는 함수
}
```

##### (4) 매핑(Mapping)
key, value 페어로 저장

```solidity
pragma solidity ^0.4.21;

// 직장(Workplace) Contract 생성
contract Workplace {

  mapping(string => string) employees;
  // function getEmployees() -> 강의자를 불러오는 함수
}
```

##### (5) 구조체(Struct)
사용자 정의 자료형

```solidity
pragma solidity ^0.4.21;

// 사람(People) Contract 생성
contract People {

  struct Person {
    string name;
    uint age;
  }

  Person[] people;
}
```

### 실전 DApp 만들기 : 저녁메뉴 추천 Contract

```solidity
pragma solidity ^0.4.21;

contract Menu {

    string[] list = ["짜장면","탕수육","볶음밥","짬뽕","냉면"];
    uint[] result;

    function random() public view returns (uint) {
        return uint(uint256(keccak256(block.timestamp, now)) % list.length);
    }

    function pick() public view returns(string) {
        return list[random()];
    }

}
```

## 참고 및 주의사항

### Gas system

| gasPrice                                                     | startGas/gasLimit                                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1 gas unit 의 **wei** 가격. (특정 contract 를 진행하기 위해서) | 해당 transaction 을 진행하기 위해서 사용할 수 있는 gas 의 총량 |

* 1 Ether == 1,000,000,000,000,000,000 Wei


### Metamask's Mnemonic Phrases (12 secret words)

* 반드시 저장할 것.
* https://iancoleman.io/bip39/
  * 위 주소에 mnemonic phrases 를 입력하고, 코인을 Ethereum 으로 바꾸면 아래 Derived Addresses 에 본인 계정들이 나온다. 현재 default 계정의 Address 와 표시된 첫 번째 Address 를 확인.

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
