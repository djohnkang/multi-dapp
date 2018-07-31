# Geth & Mist

## 1. Geth
Go ethereum client
- Ethereum은 하나의 protocol
- 다양한 언어로 구현 가능(C++, Python, Javascript)
- Ethereum 재단에서 개발한 Go 언어 기반 클라이언트
- Go 언어를 몰라도 사용가능
- console은 `web3`(javascript)

### Geth 설치하기

1. 다운로드 받기 : https://geth.ethereum.org/downloads/

2. `cmd`를 통해 설치 확인
```shell
> geth version
```

### Geth로 나만의 Ethereum Network 구현하기
1. Ethereum Network 데이터를 저장할 폴더를 생성
```shell
> mkdir eth
> cd eth
```

2. `genesis.json` :  제네시스 블럭 생성
```
{
  "config": {
    "chainId": 1202,
    "homesteadBlock": 0,
    "eip155Block": 0,
    "eip158Block": 0
  },
  "nonce": "0x0000000000000033",
  "timestamp": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x8000000",
  "difficulty": "0x100",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {}
}
```

3. 네트워크 시작하기

```shell
> geth --datadir ./data init genesis.json
```

4. 네트워크 실행하기(console)

```shell
> geth --datadir ./data --networkid 1202 console
```

5. 계정 만들기

```shell
// 계정 만들기
> personal.newAccount("비밀번호")

// 계정 확인하기
> eth.accounts
> eth.accounts[0]

// Etherbase 코인베이스
> eth.coinbase

// Ether 잔액 확인
> eth.getBalance(eth.accounts[0])
```

6. 채굴하기

```shell
// 채굴하기
miner.start(1)

// 채굴중인지 확인
> eth.mining
true

> eth.blockNumber

// Ether 잔액 확인
> eth.getBalance(eth.accounts[0])

> web3.fromWei(eth.getBalance(eth.accounts[0]),”ether”)

> miner.stop()
```

7. Ether 주고 받기

```shell
// Ether 보내는 방법
> eth.sendTransaction(
  {
    from: 누구로부터,
    to: 누구에게,
    value: 얼만큼
  }
)

// Ether 보내기
eth.sendTransaction({from:eth.accounts[0], to:eth.accounts[1], value:web3.toWei(5,"ether")})

에러 !

// 잠금해제 personal.unlockAccount(eth.accounts[0],"비밀번호")

// 보류중인 거래들
eth.pendingTransactions

// 다시 마이닝
miner.start(1)

// 거래보기
eth.getTransaction("Tx Hash")
```

## 2. Mist
**이제는 잘 쓰이지 않는** Ethereum 그래픽 인터페이스

### Mist 설치하기
1. 다운로드 받기 :  https://github.com/ethereum/mist/releases

2. 실행하기
