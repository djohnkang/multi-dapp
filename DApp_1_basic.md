# 실전 DApp 강의 - basic
Ethereum과 Interaction하는 방법들

## Ethereum 이란 무엇인가.

* Ethereum 네트워크는 돈을 전송하고, 그 데이터를 저장한다.
* 세상에는 엄청나게 많은 Ethereum 네트워크가 존재한다.
  * 메인 네트워크가 존재하지만, 따로 존재하는 많은 네트워크가 존재.
  * 강의 진행중에 우리도 작은 네트워크를 만들어 볼 예정
* Ethereum 네트워크는 한개 혹은 이상의 Node 로 이루어져 있다.
* 여기서 Node 는 Ethereum 클라이언트를 실행하는 모든 종류의 컴퓨터를 의미한다.
  * 즉 컴퓨터에 Ethereum 프로그램을 설치하면 Ethereum Node 가 된다.
* Ethereum Node 는 누구나 돌릴 수 있다.
* 모든 Ethereum Node는 완전한 연속된 블록들(**Blockchain**)을 복사해서 저장한다.
* **Blockchain**은 일어난 모든 거래(transaction)들의 레코드를 저장한 **DB** 이다.

### 결론: Blockchain == DB


## Etherium Network 사용하기.

* 개발자
  * web3.js
* 사용자
  * Metamask : 하나의 계정으로 모든 네트워크 사용
    * 가입 & 12words 저장.
    * Ropsetn Test Network 로 이동.
    * https://faucet.metamask.io/ 에서 ether 받기.

## What is Transaction?

* https://faucet.metamask.io/ 에서 주소를 입력했을 때 실제로 일어난 일들.

  1. `request 1 ether from faucet` 누름

  2. address 가 서버로 제출

  3. 서버가 `web3.js` 라이브러리를 사용하여 `transaction` Object를생성

     * Transaction : 한 계정에서 다른 계정으로 돈을 보내는 행위 (Blockchain 의 데이터 변화 그 자체)

     * 송금(money sending) Transaction 의 구성

       | name              | Description                                                  |
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

     1. `transaction` 이 Ethereum 네트워크로 (특정 Node로) 전송됨.
     2. 해당 Node 가 우리가 전송한 `transaction` 이외의 다른 `transaction` 들을 모아서  Block 으로 만듬.
     3. Node 가 유효성검사(Validation Logic)를 통해 해당 Transaction 들을 인증(confirm) 함.
     4. Target 보다 낮은 숫자가 되도록 Nonce 값을 조정함.
     5. 찾는다면 Block 이 생성됨.
     6. **위 1 ~ 5 까지의 process 가 실행되는데 걸리는 시간이 Blocktime 이며, 일반적으로 15초정도 걸린다. 이유는 Blockchain 네트워크가 평균시간을 15초에 수렴하도록 하기위해 Target 을 통해 난이도를 조절하기 때문.**
     7. 앞으로 진행할 Locat Network 에서는 시간이 거의 걸리지 않을것. 단순히 편한 개발을 위함.

  6. 완료!



##  Smart Contract

* 코드에 의해 관리되는 계약.

* Contract Account 의 구성

  | Field   | Description                          |
  | ------- | ------------------------------------ |
  | Balance | Account 가 가지고 있는 Ether 양.     |
  | Storage | 해당 거래의 데이터를 저장하는 저장고 |
  | Code    | 해당 거래를 위한 기계어 코드         |



## First Contract

* Remix

```solidity
// 사용할 solidity version 명시
pragma solidity ^0.4.17;

// 새로운 Contract 를 정의(Class 로 생각하자.)
contract Note {

	// Contract 에서 존재할 모든 Instance 변수를 선언. (Staorage Variable)
    string public message;

    // contract 이름과 같음 = 생성자 function
    function Note(string initialMessage) public {
        message = initialMessage;

    }

    function setNote(string newMessage) public {
        message = newMessage;
    }

    // getMessage() => function name | public view => function type | return 할 대상의 type
    // 사실 Starage Variable 은 생성되는 순간 변수 이름과 같은 return function 이 생성됨.
    // 아래 function 은 이미 존재하는 function 을 굳이 또 적은것.
    function getMessage() public view returns(string) {
        return message;
    }
}
```

| Function type | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| public        | 누구나 호출 할 수 있는 function                              |
| private       | 해당 Contract 내에서만 호출 할 수 있는 function              |
| **view**      | 이 function 은 data 를 return 하며, Contract 의 data 를 수정하지 않는다. |
| constant      | 이 function 은 data 를 return 하며, Contract 의 data 를 수정하지 않는다. |
| pure          | 이 function 은 Contract 의 데이터를 수정하거나, 읽지 않는다. |
| payable       | 누군가 이 function 을 호출하면, ether 를 함께 보낼 수도 있다. |



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

* Contract function 의 실행

  | function 호출                      | function 을 통해 transaction 이 일어남. |
  | ---------------------------------- | --------------------------------------- |
  | Contract 의 data 를 수정할 수 없음 | Contract 의 data 를 수정할 수 있음      |
  | 바로 실행됨                        | 실행에 시간이 걸림                      |
  | Data 를 리턴 가능                  | transaction 의 hash 값을 return         |
  | 무료                               | 유료                                    |



### Wei vs Ether

1 Ether == 1,000,000,000,000,000,000 Wei

### Gas system

| gasPrice                                                     | starGas/gasLimit                                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1 gas unit 의 **wei** 가격. (특정 contract 를 진행하기 위해서) | 해당 transaction 을 진행하기 위해서 사용할 수 있는 gas 의 총량 |

* Remix

```solidity
pragma solidity ^0.4.17;

contract Note {

    string public message;

    function Note(string initialMessage) public {
        message = initialMessage;    
    }

    function setMessage(string newMessage) public {
        message = newMessage;
    }

    function doMath(int a, int b) {
    		   // 필요한 Gas
        a + b;  // 3
        b - a;  // 3
        a * b;  // 5
        a == 0; // 3
    }

}
```



## Mnemonic Phrases (12 secret words)

* 반드시 저장할 것.
* https://iancoleman.io/bip39/
  * 위 주소에 mnemonic phrases 를 입력하고, 코인을 Ethereum 으로 바꾸면 아래 Derived Addresses 에 본인 계정들이 나온다. 현재 default 계정의 Address 와 표시된 첫 번째 Address 를 확인.

## Authors
Translated, edited, and written by
- 강동주 [@djohnkang]('https://github.com/djohnkang')
- 유태영 [@neovansoarer]('https://github.com/neovansoarer')
- 오창희 [@5chang2]('https://github.com/5chang2')

## Acknowledgement
- Stephen Grider [@StephenGrider]('https://github.com/StephenGrider')
  - [ethereum and solidity the complete developer's guide]('https://www.udemy.com/ethereum-and-solidity-the-complete-developers-guide/learn/v4/t/lecture/9025578?start=1')

## License
Under the [MIT License](https://opensource.org/licenses/MIT).
