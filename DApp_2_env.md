# 로컬 환경 설정
DApp 개발을 위해 필요한 기본적인 로컬 환경을 구축한다.

## Node 구축 실습과 DApp 개발에 필요한 기초 프로그램
(`chocolatey`, `git`, `vscode`) 설치하기

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
- VCS(버전관리도구)
- 개발을 위해 필수적인 도구

```shell
> choco install git -y
> git --version
git version 2.17.0.windows.1
```

### 4. `Visual Studio Code` (vscode) 설치
- 코드 에디터(코드 작성 도구)
- Solidity & Javascript 코드 작성을 위한 tool

```shell
> choco install vscode -y
> code --version
1.23.1
# 시간이 좀 오래 걸립니다ㅜㅜ
```
