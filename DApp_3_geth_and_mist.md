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

2.

## 2. Mist

### Mist 설치하기
