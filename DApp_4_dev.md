# 개발 및 배포 환경 구축
DApp 개발(Solidity 코드 작성, 테스트, 배포)을 위한 개발 및 배포 환경 구축 

## Contract Development & Deployment (Contract 개발 및 배포)

  * **Contract source -> Solidity Compiler -> ABI & Contract Bytecode -> Ropsten Network**

  * Remix : 코드 작성 및 배포 가능, but test가 어려움

  * Testing을 위한 로컬 환경설정 필요

## 개발 및 배포 환경 설정이 필요한 이유

* Boilerplate Destign (JS <=> Solidity)

| Issue                                                   | Solution                                                 |
| ------------------------------------------------------- | -------------------------------------------------------- |
| solidity 코드를 JS 프로젝트 안에 작성할 수 있어야 한다. | Solidity 컴파일러를 설치한다.                            |
| Remix 와 달리 GUI가 아닌 자동화된 test 가 필요하다.     | Custom `Mocha` 테스트로 어떻게든 Solidity 코드를 테스트. |
| public network 에 contract 를 배포하기.                 | 컴파일+배포용 스크립트를 작성한다.                       |

### 1. `Node.js` 설치
- V8 Engine javascript runtime
- Ethereum DApp 개발을 위해 가장 중요한 `web3` library를 활용하기 위함

```shell
> choco install nodejs -y
> node --version
v10.1.0 # 반드시 8 이상.
```

### 2. `windows-build-tools` 설치
- native Node module들을 컴파일 하기 위해 필요
- `web3` 라이브러리를 활용하기 위해 필수적으로 설치해야함

```shell
> npm install --global --production windows-build-tools
# 이 친구도 시간이 좀 오래 걸립니다ㅜㅜ
```

### 3. `ganache` 설치
- https://truffleframework.com/ganache 에서 다운로드 후 설치
- 로컬 Ethereum test network를 구축


### 4. DApp 개발 Project Boilerplate
프로젝트 구성을 위한 파일 및 폴더 구조

#### (1) 프로젝트 폴더 생성

```shell
> mkdir dapp
> cd dapp
> mkdir hello
> cd hello

> pwd
C:₩Users₩[Username]₩dapp₩hello

> mkdir contracts
# solidity 코드를 관리할 폴더 생성
```

#### (2) `npm`을 통한 javascript dependency 관리 
- 
```shell
> npm init

# Enter 여러번 입력

> ls
package.json
```

#### (3) npm modules들 설치하기

1. `solc` 솔리디티 컴파일러 : solidity 코드를 bytecode로 컴파일해주는 라이브러리

```shell
> npm install --save solc
```

2. `web3` 

```shell
> npm install --save web3
```

3. `ganache-cli`

```shell
> npm install --save ganache-cli
```

4. 한 번에 설치하기

```shell
> npm install --save solc web3 ganache-cli 
```