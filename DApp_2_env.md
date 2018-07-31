# 로컬 개발 환경 설정

## Contract Deployment (Contract 배포)

  * **Contract source -> Solidity Compiler -> ABI & Contract Bytecode -> Ropsten Network**

  * Remix : 코드 작성 및 배포 가능, but test가 어려움

  * Testing을 위한 로컬 환경설정 필요

## 개발 환경 설정이 필요한 이유

* Boilerplate Destign (JS <=> Solidity)

| Issue                                                   | Solution                                                 |
| ------------------------------------------------------- | -------------------------------------------------------- |
| solidity 코드를 JS 프로젝트 안에 작성할 수 있어야 한다. | Solidity 컴파일러를 설치한다.                            |
| Remix 와 달리 GUI가 아닌 자동화된 test 가 필요하다.     | Custom `Mocha` 테스트로 어떻게든 Solidity 코드를 테스트. |
| public network 에 contract 를 배포하기.                 | 컴파일+배포용 스크립트를 작성한다.                       |

## 개발 환경 설정
개발에 필요한 다양한 프로그램 (`chocolatey`, `git`, `nodejs`, `vscode`, `windows-build-tools` ) 설치하기

### 1. `cmd` 실행
- windows 실행에서 `cmd` 입력
- **관리자 권한** 으로 실행하여야 한다.

### 2. `Chocolatey` 설치
- https://chocolatey.org/install
- **Install with cmd.exe** 의 코드를 복사하여 cmd 창에 붙여 넣는다.

```shell
> @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
> choco -v
0.10.11
```

### 3. `Git` 설치
-
-

```shell
> choco install git -y
> git --version
git version 2.17.0.windows.1
```

### 4. `Visual Studio Code` (vscode) 설치
-
-

```shell
> choco install vscode -y
> code --version
1.23.1
# 시간이 좀 오래 걸립니다ㅜㅜ
```

### 5. `Node.js` 설치

```shell
> choco install nodejs -y
> node --version
v10.1.0 # 반드시 8 이상.
```

### 6. `windows-build-tools` 설치
- native Node module들을 컴파일 하기 위해 필요
- `web3` 라이브러리를 활용하기 위해 필수적으로 설치해야함

```shell
> npm install --global --production windows-build-tools
# 이 친구도 시간이 좀 오래 걸립니다ㅜㅜ
```

### 7.
