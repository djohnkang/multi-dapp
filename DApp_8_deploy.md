# Ethereum Network에 Contract 배포하기
infura.io 서비스를 통한 컨트랙트 실전 배포

### 1. https://infura.io 가입 및 네트워크 보기.

### 2. `truffle-hdwallet-provider` 설치

```powershell
note> npm install --save truffle-hdwallet-provider
```

### 3. `note\deploy.js`

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

* Console 에 Contract Address 가 나타난다면, https://ropsten.etherscan.io/address/[contract_address] 를 입력하고 실제 배포되었는지 확인해보자.