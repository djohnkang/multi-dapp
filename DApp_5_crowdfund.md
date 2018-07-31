# 실전 DApp 강의 - Crowdfund
크라우드펀딩 DApp

### 0. 개요
기존 크라우드 펀딩의 문제점을 보완하는 블록체인 기반의 크라우드펀딩 DApp

### 1. Campaign

- 먼저 remix에서 코드를 작성합니다.

- Campaign contract 를 작성합니다.

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    // Campaign의 관리자와 최소 후원금액을 설정하는 변수를 선언합니다.
    address public manager;
    uint public minimumContribution;

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
}
```

- 후원을 위한 함수를 작성합니다.

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    address public manager;
    uint public minimumContribution;
    // 후원을 한 사람들을 저장할 변수인 address타입의 동적 배열 approovers를 선언합니다.
    address[] public approvers;

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    // 지불을 할 수 있게 payable 타입을 추가해서 contribute 함수를 생성합니다.
    function contribute() public payable {
        // Campaign을 생성했을때의 최소 후원 금액보다 큰 값을 value로 주어야 실행 가능합니다.
        require(msg.value > minimumContribution);
        // 후원자의 주소를 approvers 변수에 저장합니다.
        approvers.push(msg.sender);
    }
}
```

- 테스트를 해봅시다.
- Request 구조체를 만들어봅시다.

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    // Request 구조체를 선언합니다.
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
    }

    address public manager;
    uint public minimumContribution;
    address[] public approvers;

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers.push(msg.sender);
    }
}
```

- requests 생성

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
    }
    // Request타입의 동적배열 requests를 선언한다.
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    address[] public approvers;

    // 호출을 한 유저가 manager인지 확인하는 함수를 작성한다.
    // modifier로 만들어 다른함수에 추가해서 코드실행전에 사용한다.
    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers.push(msg.sender);
    }
}
```

- createRequest() 함수를 생성한다.
- 스토리지: 상태 변수, 함수 내 로컬 변수
  메모리: 함수의 매개 변수, 함수의 리턴값

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    address[] public approvers;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers.push(msg.sender);
    }
    // Request요청은 manager만 할 수 있고 새로만든 Request는 requests배열에 넣는 함수를 생성한다.
    function createRequest(string description, uint value, address recipient) public restricted {
        // memory 키워드 사용
        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false           
        });
        requests.push(newRequest);
    }
}
```

- mapping

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    // address[] public approvers;
    // approvers에 true,false값을 추가하기 위해 mapping타입으로 바꿔준다.
    mapping(address => bool) public approvers;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        // approvers.push(msg.sender);
        // msg.sender를 키로 만들어 true값과 함께 mapping에 추가한다.
        approvers[msg.sender] = true;
    }
    function createRequest(string description, uint value, address recipient) public restricted {
        // 코드삭제. 
        // require(approvers[msg.sender]);

        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false           
        });
        requests.push(newRequest);
    }
}
```

- 투표를 위한 함수를 작성한다.

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        // 투표카운팅을 위한 approvalCount와
        // 투표한 사람을 저장하기 위한 approvals를 선언한다.
        uint approvalCount;
        mapping(address => bool) approvals;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers[msg.sender] = true;
    }
    function createRequest(string description, uint value, address recipient) public restricted {
        //require(approvers[msg.sender]);

        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false,
           // 투표 카운팅을 위해 추가했기 때문에 객체를 만들때 0으로 초기화 해준다.
           // value type만 초기화를 해주면 되기 때문에 mapping인 approvals는 추가하지 않아도 된다.
           approvalCount: 0
        });
        requests.push(newRequest);
    }
}
```

- approveRequest

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        uint approvalCount;
        mapping(address => bool) approvals;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers[msg.sender] = true;
    }
    function createRequest(string description, uint value, address recipient) public restricted {
        //require(approvers[msg.sender]);

        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false,
           approvalCount: 0
        });
        requests.push(newRequest);
    }

    function approveRequest(uint index) public {
        // 후원을 한사람만 approveRequest를 할 수 있습니다.
        require(approvers[msg.sender]);
        // 한번 투표를 한사람은 다시 투표를 할 수 없습니다.
        require(!requests[index].approvals[msg.sender]);

        // requests중 파라미터로 들어온 index의 요청에 투표를 합니다.
        requests[index].approvals[msg.sender] = true;
        // 전체 투표 횟수를 증가시킵니다.
        requests[index].approvalCount++;

    }
}
```

- 간소화

```solidity
    function approveRequest(uint index) public {
        // 위에서 작성했던 코드중 중복된 부분을 다음과 같이 바꿀 수 있습니다.
        // 변수의 저장은 storage로 합니다.
        Request storage request = requests[index];

        require(approvers[msg.sender]);
        require(!request.approvals[msg.sender]);

        request.approvals[msg.sender] = true;
        request.approvalCount++;

    }
```

- 중간테스트 - 강의 121
- request 종료

```solidity
pragma solidity ^0.4.21;

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        uint approvalCount;
        mapping(address => bool) approvals;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;
    // Campaign에 후원한 전체 숫자를 카운팅 하기 위한 approversCount를 선언합니다.
    uint public approversCount;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }
    
    constructor(uint minimum) public {
        manager = msg.sender;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers[msg.sender] = true;
        // 후원을 하면 후원자의 전체 수를 증가시킵니다.
        approversCount++;
    }
    function createRequest(string description, uint value, address recipient) public restricted {
        //require(approvers[msg.sender]);

        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false,
           approvalCount: 0
        });
        requests.push(newRequest);
    }

    function approveRequest(uint index) public {
        Request storage request = requests[index];

        require(approvers[msg.sender]);
        require(!request.approvals[msg.sender]);

        request.approvals[msg.sender] = true;
        request.approvalCount++;

    }
    // manager만 실행할 수 있는 finalizeRequest 함수를 생성합니다.
    function finalizeRequest(uint index) public restricted {
        // 코드의 반복을 최소화 하기 위한 코드
        Request storage request = requests[index];

        // 투표가 과반이 넘어야 실행 가능
        require(request.approvalCount > (approversCount /2 ));
        // request.complete가 이미 true가 아니면 실행 가능
        require(!request.complete);
        // 이 request의 value를 recipient로 정한 주소로 전송합니다.
        request.recipient.transfer(request.value);
        // 완료여부를 true로 바꿔줍니다.
        request.complete = true;
    }
}
```

- 여러개의 캠페인

```solidity
pragma solidity ^0.4.21;

// 각각의 Campaign을 만드는 contract를 작성합니다.
contract CampaignFactory {
    // 각각의 Campaign들을 저장할 address 타입의 동적배열을 선언합니다.
    address[] public deployedCampaigns;

    // Campaign을 생성하는 함수를 작성합니다.
    function createCampaign(uint minimum) public {
        // 하나의 Campaign을 생성할 코드를 작성합니다.
        // 여기에 최소금액과 Campaign을 생성한 유저(msg.sender)를 manager로 등록합니다.
        address newCampaign = new Campaign(minimum, msg.sender);
        // 새로 만들어진 Campaign을 목록에 추가합니다.
        deployedCampaigns.push(newCampaign);
    }
    // deployedCampaigns을 출력하는 함수를 작성합니다.
    function getDeployedCampaigns() public view returns (address[]) {
        return deployedCampaigns;
    }
}

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        uint approvalCount;
        mapping(address => bool) approvals;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;
    uint public approversCount;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    // Campaign을 생성한 유저의 주소를 넣기 위해 creator를 파라미터로 받고 manager로 등록합니다.
    constructor(uint minimum, address creator) public {
        manager = creator;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers[msg.sender] = true;
        approversCount++;
    }
    function createRequest(string description, uint value, address recipient) public restricted {
        //require(approvers[msg.sender]);

        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false,
           approvalCount: 0
        });
        requests.push(newRequest);
    }

    function approveRequest(uint index) public {
        Request storage request = requests[index];

        require(approvers[msg.sender]);
        require(!request.approvals[msg.sender]);

        request.approvals[msg.sender] = true;
        request.approvalCount++;

    }
    function finalizeRequest(uint index) public restricted {
        Request storage request = requests[index];

        require(request.approvalCount > (approversCount /2 ));
        require(!request.complete);

        request.recipient.transfer(request.value);
        request.complete = true;
    }
}
```

- 테스트를 해봅시다 - 127강

- 전체코드

```solidity
pragma solidity ^0.4.21;

contract CampaignFactory {
    address[] public deployedCampaigns;

    function createCampaign(uint minimum) public {
        address newCampaign = new Campaign(minimum, msg.sender);
        deployedCampaigns.push(newCampaign);
    }
    function getDeployedCampaigns() public view returns (address[]) {
        return deployedCampaigns;
    }
}

contract Campaign {
    struct Request {
        string description;
        uint value;
        address recipient;
        bool complete;
        uint approvalCount;
        mapping(address => bool) approvals;
    }
    Request[] public requests;
    address public manager;
    uint public minimumContribution;
    mapping(address => bool) public approvers;
    uint public approversCount;

    modifier restricted() {
        require(msg.sender == manager);
        _;
    }

    constructor(uint minimum, address creator) public {
        manager = creator;
        minimumContribution = minimum;
    }
    function contribute() public payable {
        require(msg.value > minimumContribution);
        approvers[msg.sender] = true;
        approversCount++;
    }
    function createRequest(string description, uint value, address recipient) public restricted {
        //require(approvers[msg.sender]);

        Request memory newRequest = Request({
           description: description,
           value: value,
           recipient: recipient,
           complete: false,
           approvalCount: 0
        });
        requests.push(newRequest);
    }

    function approveRequest(uint index) public {
        Request storage request = requests[index];

        require(approvers[msg.sender]);
        require(!request.approvals[msg.sender]);

        request.approvals[msg.sender] = true;
        request.approvalCount++;

    }
    function finalizeRequest(uint index) public restricted {
        Request storage request = requests[index];

        require(request.approvalCount > (approversCount /2 ));
        require(!request.complete);

        request.recipient.transfer(request.value);
        request.complete = true;
    }
}
```

### 2. test

- `mkdir crowdfund && cd crowdfund`
- `npm init`
- `npm install --save ganache-cli mocha solc fs-extra web3@1.0.0-beta.26 `
- 폴더 구조는 다음과 같습니다.

```
/ethereum
	/build
	/contracts
		- Campaign.sol (remix에서 작성한 코드를 그대로 복붙)
	- compile.js
	- deploy.js
/test
	- Campaign.test.js
package.json
```

- `deploy.js` 를 작성해봅시다. - 강의 132

```javascript
const path = require('path');
const solc = require('solc');
const fs = require('fs-extra');

const buildPath = path.resolve(__dirname, 'build');
fs.removeSync(buildPath);

const campaignPath = path.resolve(__dirname, 'contracts', 'Campaign.sol');
const source = fs.readFileSync(campaignPath, 'utf8');
const output = solc.compile(source, 1).contracts;

fs.ensureDirSync(buildPath);

// console.log(output);
// output을 살펴 보면 두개의 덩어리로 나눠져 있다
// :Campaign, :CampaignFactory
// 이 두개가 for in문 안에서 각각의 json파일로 build 폴더에 저장된다.
for (let contract in output) {
  fs.outputJsonSync(
    // 파일앞에 콜론을 없애기 위한 코드
    path.resolve(buildPath, contract.replace(':', '') + '.json'),
    output[contract]
  );
}
```

- `Campaign.test.js` 파일을 작성합니다. 테스트를 위한 셋팅입니다.

```javascript
const assert = require('assert');
const ganache = require('ganache-cli');
const Web3 = require('web3');
const web3 = new Web3(ganache.provider());

const compiledFactory = require('../ethereum/build/CampaignFactory.json');
const compiledCampaign = require('../ethereum/build/Campaign.json');

let accounts;
let factory;
let campaignAddress;
let campaign;

beforeEach(async () => {
  accounts = await web3.eth.getAccounts();

  factory = await new web3.eth.Contract(JSON.parse(compiledFactory.interface))
  .deploy({ data: compiledFactory.bytecode })
  .send({ from: accounts[0], gas: '1000000' });

  await factory.methods.createCampaign('100').send({
    from: accounts[0],
    gas: '1000000'
  });

  [campaignAddress] = await factory.methods.getDeployedCampaigns().call();
  // 아래의 코드와 같습니다.
  // const addresses = await factory.methods.getDeployedCampaigns().call();
  // campaignAddress = addresses[0];
  campaign = await new web3.eth.Contract(
    JSON.parse(compiledCampaign.interface),
    campaignAddress
  );
});
```

- 테스트 코드를 붙여봅시다.

```javascript
// 위의 코드에서 계속

describe('Campaigns', () => {
  it('deploysa factory and a campaign', () => {
    assert.ok(factory.options.address);
    assert.ok(campaign.options.address);
  });
});
```

- 테스트 명령어를 위해 `package.json` 를 수정합니다.

```json
//"scripts": {
//    "test": "echo \"Error: no test specified\" && exit 1"
//  }
"scripts": {
    "test": "mocha"
  }
```

- `npm run test` 명령어로 테스트를 진행합니다.
- describe 안에 테스트를 작성합니다.

```javascript
  it('marks caller as the campaign manager', async () => {
    const manager = await campaign.methods.manager().call();
    assert.equal(accounts[0], manager);
  });

  it('allows people to contribute money and marks them as approvers', async () => {
    await campaign.methods.contribute().send({
      value: '200',
      from: accounts[1]
    });
    const isContributor = await campaign.methods.approvers(accounts[1]).call();
    assert(isContributor);
  });

  it('requires a minimum contribution', async () => {
    try {
        await campaign.methods.contribute().send({
            from: accounts[1],
            value: 5
        });
    } catch(err) {
        assert(err);
        return;
    }
    assert(false);
  });

  it('allows a manager to make a payment request', async () => {
    await campaign.methods
    .createRequest('Buy batteries', '100', accounts[1])
    .send({
      from: accounts[0],
      gas: '1000000'
    });
    const request = await campaign.methods.requests(0).call();
    assert.equal('Buy batteries', request.description);
  });

  it('processes requests', async () => {
    await campaign.methods.contribute().send({
      from: accounts[0],
      value: web3.utils.toWei('10','ether')
    });

    await campaign.methods
    .createRequest('A', web3.utils.toWei('5','ether'), accounts[1])
    .send({ from: accounts[0], gas: '1000000'});

    await campaign.methods.approveRequest(0).send({
      from: accounts[0],
      gas: '1000000'
    });

    await campaign.methods.finalizeRequest(0).send({
      from: accounts[0],
      gas: '1000000'
    });

    let balance = await web3.eth.getBalance(accounts[1]);
    balance = web3.utils.fromWei(balance, 'ether');

    balance = parseFloat(balance);
    //console.log(balance);
    assert(balance > 104);
  });
```

### 3. deploy

- `deploy.js` 파일을 작성합니다.

```javascript
const HDWalletProvider = require('truffle-hdwallet-provider');
const Web3 = require('web3');
//기존 deploy코드에서 build 폴더로 경로가 바뀌었습니다.
const compiledFactory = require('./build/CampaignFactory.json');

const provider = new HDWalletProvider(
  'news possible plate width account payment exchange car tray neglect jacket wealth',
  'https://rinkeby.infura.io/J9ezcxyo8pwuP3rWnsP6'
);

const web3 = new Web3(provider);

const deploy = async () => {
  const accounts = await web3.eth.getAccounts();

  console.log('account : ', accounts[0]);

  const result = await new web3.eth.Contract(JSON.parse(compiledFactory.interface))
  .deploy({ data: '0x' + compiledFactory.bytecode })
  .send({ gas: '1000000', from: accounts[0] });

  console.log('contract deployed : ', result.options.address);
};
deploy();
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
