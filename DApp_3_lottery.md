# 실전 DApp 강의 - Lottery
이더리움 기반의 Game DApp

## The Lottery Contract

* Solidity Code 의 구성

    | Variables name | Purpose                                        |
    | -------------- | ---------------------------------------------- |
    | manager        | Contract 를 생성하는 사람의 **주소**           |
    | players        | 참여(enter)한 사람들의 **주소**를 저장한 array |

    | Functions name | Purpose                                    |
    | -------------- | ------------------------------------------ |
    | enter          | Lottery 에 player 가 참여                  |
    | pickWinner     | 무작위로 winner 를 뽑아 prize pool 로 전송 |
    | random         | random 한 정수 뽑기 (no `random()`         |

* Solidity 의 Basic Data types

    | Name         | Descriptions                            | Examples                  |
    | ------------ | --------------------------------------- | ------------------------- |
    | string       | 연속된 문자들                           | `"Hack"` `"Life"`         |
    | bool         | 참/거짓 값                              | `true` `false`            |
    | int          | 정수값, 음수/양수, 소숫점 없음          | `0` `-30000` `59158`      |
    | uint         | 'Unsigned' int, 양수, 소숫점 없음       | `0` `30000` `999910`      |
    | fixed/ufixed | 'Fixed' point number. 소숫점 있음       | `10.1`, `-42.2` `3.14`    |
    | address      | Money 를 전송하는 메서드가 포함되어있음 | `0x134bae199c8dbae188c8d` |

* remix 에 작성해 볼 코드

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;

        constructor() public {
            // manager 의 address 가 생성될 때 필요함.
            // 'msg' 라는 global var 을 통해 알 수 있음.
        }
    }
    ```

* `msg` Global Variable

    | Property Name | Descriptions                                                 |
    | ------------- | ------------------------------------------------------------ |
    | `msg.data`    | 현재 호출된 function call 이나 transaction 의 'data' 필드    |
    | `msg.gas`     | 현재 호출된 function/transaction 의 사용가능한 gas 양        |
    | `msg.sender`  | 현재 function/transaction을 호출한 account 의 주소           |
    | `msg.value`   | function/transaction 호출과 함께 전송된 ether 의 양(wei 로 표시) |

* remix

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;

        constructor() public {
            manager = msg.sender;
        }
    }
    ```



* Array 알아보기.

    | 이름          | Notes                                                        | Examples                                               |
    | ------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
    | fixed array   | 특정 자료형만 담는 arr. 고정된 길이를 가짐.                  | `int[3]` => `[1,2,3]` `bool[2]` => `[true, false]`     |
    | dynamic array | 특정 자료형만 담는 arr, size 가 바뀜                         | `int[]` , `bool[]`                                     |
    | mapping       | Key - Value pairs. key 들끼리, value들 끼리는 data type 이 같다. | `mapping(string => string)` `mapping(int => bool)`     |
    | struct        | 서로 다른 data type 을 가질 수 있는 key - value pairs        | `struct Car { string make; string model; uint value;}` |

    * 주의할 점!
      1. Sol 에서는 n 차원 dynamic arr 가 문제가 되지 않는다. 하지만 ABI/JS/Web3 로 올 경우(Sol <=> JS 사이의 이슈) , n 차원 dynamic arr 가 불가능.
      2. 아래에서 require() 과 관련된 error 가 난다면, 왜 err 가 났는지 말해 주지 않음..

* Remix - `enter()` function 만들기

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        address[] public players;

        constructor() public {
            manger = msg.sender;
        }

        function enter() public payable {
        	// require() : Global function.
        	// require(bool expression) 에서 false 가 되면, 전체 function 이 stop.
        	require(msg.value > .01 ether);
            players.push(msg.sender);
        }
    }
    ```

* Remix - `random()`  function 만들기

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        address[] public players;

        constructor() public {
            manger = msg.sender;
        }

        function enter() public payable {
        	require(msg.value > .01 ether);
            players.push(msg.sender);
        }

        function random() private view returns (uint) {
        	// sha3() / keccak256() 도 require() 처럼 global function
            return uint(keccak256(block.difficulty, now, players));
        }
    }
    ```

* Remix - `pickWinner()` function 만들기.

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        address[] public players;

        constructor() public {
            manager = msg.sender;
        }

        function enter() public payable {
            require(msg.value > 0.01 ether);
            players.push(msg.sender);
        }

        function random() private view returns (uint) {
            return uint(keccak256(block.difficulty, now, players));
        }

        function pickWinner() public {       
            require(msg.sender == manager); // 우선 pickWinner() function 호출자 제한(manager)
            uint index = random() % players.length;
            players[index].transfer(this.balance); // 현재 contract 의 모든 balance 보내기
            players = new address[](0); // players 를 처음의 empty dynamic array 로 변경.
        }
    }
    ```

* Remix - 중복코드 제거용 `modifier` 생성

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        address[] public players;

        constructor() public {
            manager = msg.sender;
        }

        function enter() public payable {
            require(msg.value > 0.01 ether);
            players.push(msg.sender);
        }

        function random() private view returns (uint) {
            return uint(keccak256(block.difficulty, now, players));
        }

        // public 뒤에 "restricted()" modifer를 추가
        function pickWinner() public restricted {       
        	// require(msg.sender == manager); 해당 줄 삭제
            uint index = random() % players.length;
            players[index].transfer(this.balance);
            players = new address[](0);
        }

        modifier restricted() {
            require(msg.sender == manager);
            _; // function 에 restricted 키워드가 있으면 _에 해당 function 코드들이 들어간다.
        }
    }
    ```

* Remix - `getPlayers()` function 생성. (players들 전체 조회)

    ```solidity
    pragma solidity ^0.4.17;

    contract Lottery {
        address public manager;
        address[] public players;

        constructor() public {
            manager = msg.sender;
        }

        function enter() public payable {
            require(msg.value > 0.01 ether);
            players.push(msg.sender);
        }

        function random() private view returns (uint) {
            return uint(keccak256(block.difficulty, now, players));
        }

        function pickWinner() public restricted {
            uint index = random() % players.length;
            players[index].transfer(this.balance);
            players = new address[](0);
        }

        modifier restricted() {
            require(msg.sender == manager);
            _;
        }

        function getPlayers() public view returns(address[]) {
            return players
        }
    }
    ```



##  Time to test in LOCAL

1. `\note\` 디렉토리를 **copy - paste** 하여 이름을 `\lottery\` 로 바꾸기.

2. 다음 파일들을 삭제 => 생성

   * `\lottery\contracts\Inbox.sol` 삭제

   * `\lottery\contracts\Lottery.sol` 생성

     ```solidity
     pragma solidity ^0.4.17;

     contract Lottery {
         address public manager;
         address[] public players;

         constructor() public {
             manager = msg.sender;
         }

         function enter() public payable {
             require(msg.value > 0.01 ether);
             players.push(msg.sender);
         }

         function random() private view returns (uint) {
             return uint(keccak256(block.difficulty, now, players));
         }

         function pickWinner() public restricted {
             uint index = random() % players.length;
             players[index].transfer(this.balance);
             players = new address[](0);
         }

         modifier restricted() {
             require(msg.sender == manager);
             _;
         }

         function getPlayers() public view returns(address[]) {
             return players;
         }
     }
     ```

   * `\lottery\test\Inbox.test.js` 삭제

   * `\lottery\test\Lottery.test.js`  생성

3. 다음 파일들을 수정

   * `\lottery\compile.js`

     ```js
     const path = require('path');
     const fs = require('fs');
     const solc = require('solc');

     const lotteryPath = path.resolve(__dirname, 'contracts', 'Lottery.sol'); //.sol 파일 수정
     const source = fs.readFileSync(lotteryPath, 'utf8');


     module.exports = solc.compile(source, 1).contracts[':Lottery']; // key 수정
     ```

   * `\lottery\deploy.js`

     ```js
     const HDWalletProvider = require('truffle-hdwallet-provider');
     const Web3 = require('web3');
     const { interface, bytecode } = require('./compile');

     const provider = new HDWalletProvider(
         '[12 mnemonic words]',
         '[infura 주소]'
     );

     const web3 = new Web3(provider);

     const deploy = async () => {
         const accounts = await web3.eth.getAccounts();

         console.log('Attempting to deploy from account', accounts[0]);

         const result = await new web3.eth.Contract(JSON.parse(interface))
             .deploy({ data: '0x' + bytecode })
             .send({ from: accounts[0], gas: '1000000', gasPrice: web3.utils.toWei('2', 'gwei') });

       console.log('Contract deployted to', result.options.address);  
     };

     deploy();
     ```

4. `\lottery\test\Lottery.test.js` : ganache 로컬 네트워크에 배포가 정상적으로 이루어 지는지 확인.

   ```js
   const assert = require('assert');
   const ganache = require('ganache-cli');
   const Web3 = require('web3');
   const web3 = new Web3(ganache.provider());

   const { interface, bytecode } = require('../compile');

   let lottery;
   let account;

   beforeEach(async ()=> {
       accounts = await web3.eth.getAccounts();

       lottery = await new web3.eth.Contract(JSON.parse(interface))
           .deploy({ data: bytecode })
           .send({ from: accounts[0], gas: '1000000' });
   });

   describe('Lottery Contract', () => {
       it('deploys a contract', () => {
           assert.ok(lottery.options.address);
       });
   });
   ```

5. `\lottery\test\Lottery.test.js` : 하나의 account 가 참여되는지 확인.

   ```js
   const assert = require('assert');
   const ganache = require('ganache-cli');
   const Web3 = require('web3');
   const web3 = new Web3(ganache.provider());

   const { interface, bytecode } = require('../compile');

   let lottery;
   let account;

   beforeEach(async ()=> {
       accounts = await web3.eth.getAccounts();

       lottery = await new web3.eth.Contract(JSON.parse(interface))
           .deploy({ data: bytecode })
           .send({ from: accounts[0], gas: '1000000' });
   });

   describe('Lottery Contract', () => {

       // contract 배포 확인
       it('deploys a contract', () => {
           assert.ok(lottery.options.address);
       });

       // player 한명 참여 확인
       it('allows one account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether') // 0.01 보다 높아야 함.
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(1, players.length);
       });
   });
   ```

6. `\lottery\test\Lottery.test.js` : 여러 account 가 참여되는지 확인.

   ```js
   const assert = require('assert');
   const ganache = require('ganache-cli');
   const Web3 = require('web3');
   const web3 = new Web3(ganache.provider());

   const { interface, bytecode } = require('../compile');

   let lottery;
   let account;

   beforeEach(async ()=> {
       accounts = await web3.eth.getAccounts();

       lottery = await new web3.eth.Contract(JSON.parse(interface))
           .deploy({ data: bytecode })
           .send({ from: accounts[0], gas: '1000000' });
   });

   describe('Lottery Contract', () => {

       // contract 배포 확인
       it('deploys a contract', () => {
           assert.ok(lottery.options.address);
       });

       // player 한명 참여 확인
       it('allows one account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(1, players.length);
       });

   	// player 여러명 참여 확인
       it('allows multiple account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[1],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[2],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(accounts[1], players[1]);
           assert.equal(accounts[2], players[2]);

           assert.equal(3, players.length);
       });
   });
   ```

7. `\lottery\test\Lottery.test.js` : 충분한 참여 ether 를 보내지 않았을 때의 test

   ```js
   const assert = require('assert');
   const ganache = require('ganache-cli');
   const Web3 = require('web3');
   const web3 = new Web3(ganache.provider());

   const { interface, bytecode } = require('../compile');

   let lottery;
   let account;

   beforeEach(async ()=> {
       accounts = await web3.eth.getAccounts();

       lottery = await new web3.eth.Contract(JSON.parse(interface))
           .deploy({ data: bytecode })
           .send({ from: accounts[0], gas: '1000000' });
   });

   describe('Lottery Contract', () => {
       // contrat 배포 확인.
       it('deploys a contract', () => {
           assert.ok(lottery.options.address);
       });

       // player 한명 참여 확인
       it('allows one account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(1, players.length);
       });

   	// player 여러명 참여 확인
       it('allows multiple account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[1],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[2],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(accounts[1], players[1]);
           assert.equal(accounts[2], players[2]);

           assert.equal(3, players.length);
       });

       // player 가 0.01 ether 보다 적은 양을 보냈을 경우 test
       it('requires a minimum amount of ether to enter', async () => {
           try {
               await lottery.methods.enter().send({
                   from: accounts[0],
                   value: web3.utils.toWei('0.00001', 'ether')
               });
               // 여기까지 오는 도중 err 가 발생하면 catch 문으로 이동

               // 이 줄에 도달했다면, err 가 발생하지 않은 것이므로 test 실패
               assert(false);
           } catch (err) {
               // err 역시 값을 가지므로, 만약 err 가 발생했다면, test 성공
               assert(err);
           }
       });
   });
   ```

8. `\lottery\test\Lottery.test.js` :  manager 가 아닌 사람이 `pickWinner()` 를 실행할 경우를 test

   ```js
   const assert = require('assert');
   const ganache = require('ganache-cli');
   const Web3 = require('web3');
   const web3 = new Web3(ganache.provider());

   const { interface, bytecode } = require('../compile');

   let lottery;
   let account;

   beforeEach(async ()=> {
       accounts = await web3.eth.getAccounts();

       lottery = await new web3.eth.Contract(JSON.parse(interface))
           .deploy({ data: bytecode })
           .send({ from: accounts[0], gas: '1000000' });
   });

   describe('Lottery Contract', () => {
       // contrat 배포 확인.
       it('deploys a contract', () => {
           assert.ok(lottery.options.address);
       });

       // player 한명 참여 확인
       it('allows one account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(1, players.length);
       });

   	// player 여러명 참여 확인
       it('allows multiple account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[1],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[2],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(accounts[1], players[1]);
           assert.equal(accounts[2], players[2]);

           assert.equal(3, players.length);
       });

       // player 가 0.01 ether 보다 적은 양을 보냈을 경우 test
       it('requires a minimum amount of ether to enter', async () => {
           try {
               await lottery.methods.enter().send({
                   from: accounts[0],
                   value: web3.utils.toWei('0.00001', 'ether')
               });
               assert(false);
           } catch (err) {
               assert(err);
           }
       });

       // manager 가 아닌 사람이 pickWinner() 를 실행할 경우를 test
       it('only manager can call pickWinner', async () => {
           try {
               await lottery.methods.pickWinner().send({
                   // accounts[1] 은 확실히 참여하지 않았기에 manager 가 아님.
                   from: accounts[1]
               });
               assert(false);
           } catch (err) {
               assert(err);
           }
       })
   });
   ```

9. `\lottery\test\Lottery.test.js` :  winner 에게 돈을 보내고, players array가 리셋되는지 확인

   ```js
   const assert = require('assert');
   const ganache = require('ganache-cli');
   const Web3 = require('web3');
   const web3 = new Web3(ganache.provider());

   const { interface, bytecode } = require('../compile');

   let lottery;
   let account;

   beforeEach(async ()=> {
       accounts = await web3.eth.getAccounts();

       lottery = await new web3.eth.Contract(JSON.parse(interface))
           .deploy({ data: bytecode })
           .send({ from: accounts[0], gas: '1000000' });
   });

   describe('Lottery Contract', () => {
       // contrat 배포 확인.
       it('deploys a contract', () => {
           assert.ok(lottery.options.address);
       });

       // player 한명 참여 확인
       it('allows one account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(1, players.length);
       });

   	// player 여러명 참여 확인
       it('allows multiple account to enter', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[1],
               value: web3.utils.toWei('0.02', 'ether')
           });

           await lottery.methods.enter().send({
               from: accounts[2],
               value: web3.utils.toWei('0.02', 'ether')
           });

           const players = await lottery.methods.getPlayers().call({
               from: accounts[0]
           });

           assert.equal(accounts[0], players[0]);
           assert.equal(accounts[1], players[1]);
           assert.equal(accounts[2], players[2]);

           assert.equal(3, players.length);
       });

       // player 가 0.01 ether 보다 적은 양을 보냈을 경우 test
       it('requires a minimum amount of ether to enter', async () => {
           try {
               await lottery.methods.enter().send({
                   from: accounts[0],
                   value: web3.utils.toWei('0.00001', 'ether')
               });
               assert(false);
           } catch (err) {
               assert(err);
           }
       });

       // manager 가 아닌 사람이 pickWinner() 를 실행할 경우를 test
       it('only manager can call pickWinner', async () => {
           try {
               await lottery.methods.pickWinner().send({
                   from: accounts[1]
               });
               assert(false);
           } catch (err) {
               assert(err);
           }
       });

       // winner 에게 돈을 보내고, players arr 가 리셋되는지 확인
       it('sends money to the winner and resets the players array', async () => {
           await lottery.methods.enter().send({
               from: accounts[0],
               value: web3.utils.toWei('2', 'ether')
           });
           // 참가 후 잔액
           const initialBalance = await web3.eth.getBalance(accounts[0]);

           // 현재 players 의 수
           var players = await lottery.methods.getPlayers().call({ from: accounts[0] });
           assert.equal(players.length, 1)

           // winner 추첨
           await lottery.methods.pickWinner().send({ from: accounts[0] });

           // winner 가 된 이후의 잔액
           const finalBalance = await web3.eth.getBalance(accounts[0]);

           // 잔액간의 차이를 비교
           const diff = finalBalance - initialBalance;

           // gas 때문에 2 가 아니라 1.8로 차이를 설정
           assert(diff > web3.utils.toWei('1.8', 'ether'))

           // winner 를 뽑고 난 이후의 players 의 수
           players = await lottery.methods.getPlayers().call({ from: accounts[0] });
           assert.equal(players.length, 0);
       });
   });
   ```

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
