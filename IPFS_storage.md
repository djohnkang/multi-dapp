# IPFS News app

## HTTP Web 의 문제점

* 불안정하다.
  * 서버가 응답할 수 없는 상황이 되면, data에 접근 불가.
  * 특정 이유로 서버의 모든 data 가 삭제되면, 백업이 없을경우(완벽하지 않을 경우) data 의 영원한 손실.
* Hyper Centralization.
  * Web 의 태생적 목표 역시 탈 중앙화
  * 하지만 현실은 전체 사용자가 소수의 서비스의 의존
* 현재의 생태계에서 매우 비효율적.
  * YouTube 에서 10MB 짜리 영상이 1억 view 를 달성하면, 1PB(1,000,000,000mb) 의 데이터 전송이 일어남. 10mb 에 1원이 소모된다고 가정해도, 1억원이 단순 파일 분배에 소모됨.
  * 이전 시대의 콘텐츠는 용량이 크지 않았으나, 현재는 GB 단위가 흔함.
  * Bandwidth(전송량) 의 발전속도가 Storage(저장량) 을 따라가지 못함. => 전송 속도가 느려짐.
* 느리다.
  * 서버-클라이언트 간 물리적인 거리의 영향을 받음.
  * 미국 사이트 접속시 속도 느려짐
* Internet 연결에 과도한 의존성을 가진다.

## Concept of IPFS(InterPlanetary File System)

* InterPlanetary = 행성간의 컴퓨터들 까지도 모두 연결하겠다는 P2P 파일(데이터, 정보, 콘텐츠, etc..) 시스템.
* 중앙화된 서버 없이 각 노드들간의 P2P 통신으로 이루어짐.
  * 몇몇 노드들과의 연결이 끊어져도 안정적으로 유지.
* 큰 용량의 파일을 빠르고 효율적으로 전달 가능(Bitswap - BitTorrent 에서 영향을 받음.)
* IPFS 상에 업로드된 파일의 이름은 영구적으로 기록, 지키고자 하는 파일은 원하는 만큼 지킬 수 있다.
* Git 으로 버젼관리 가능
* 주류 인터넷에 연결이 안되더라도 생태계 유지 가능

## Dependencies

1. `geth`
   * Terminal 에 `geth -v` 를 통해 `geth` 설치 확인.
   * 설치되어 있지 않을 경우, https://geth.ethereum.org/downloads 에서 latest stable release  설치
2. `ipfs`
   * Terminal 에 `ipfs --version` 를 통해 `ipfs` 설치 확인.
   * 설치되어 있지 않을 경우, https://ipfs.io/docs/install 에서 go-ipfs 설치

## Instructions

1. 해당 repo 를 다운로드 한다.

2. 터미널 상에서 해당 repo 로 들어온다 (`cd`)

3. `$ geth --datadir="./" account new` => passphrase 두번 입력

4. `genesisblock.json` 이라는 파일을 생성하고 아래 내용을 저장한다.

   ```json
   {
       "config": {
           "chainID"       : 10,
           "homesteadBlock": 0,
           "eip155Block":    0,
           "eip158Block":    0
       },
       "nonce": "0x01",
       "difficulty": "0x20000",
       "mixhash": "0x00000000000000000000000000000000000000647572616c65787365646c6578",
       "coinbase": "0x0000000000000000000000000000000000000000",
       "timestamp": "0x00",
       "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
       "extraData": "0x00",
       "gasLimit": "0x2FEFD8",
       "alloc": {
       }
   }
   ```

5. 다음 명령어를 차례로 입력

   ```powershell
   $ geth --datadir="./" init genesisblock.json
   $ geth --datadir="./" --networkid 23422  --rpc --rpccorsdomain="*" --rpcport="8545" --minerthreads="1" --mine --nodiscover --maxpeers=0 --unlock 0 console
   Passphrase: # passphrase 입력
   ```

6. 새 cmd 터미널을 열고 `ipfs` 초기화와 설정 진행

   ```powershell
   $ ipfs init
   $ ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin "[\"*\"]"
   # mac/linux 의 경우 ipfs config --json API.HTTPHeaders.Access-Control-Allow-Origin '["*"]'
   $ ipfs config --json Gateway.HTTPHeaders.Access-Control-Allow-Origin "[\"*\"]"
   # mac/linux 의 경우 ipfs config --json Gateway.HTTPHeaders.Access-Control-Allow-Origin '["*"]'
   ```

7. `storage.html` 을 브라우저에서 열고 inspector 의 console 로 저혀있는 command 들을 순서대로 입력한다.

   ```js
   deployStorage() # storage 를 배포
   storeContent("https://ethereum.org/images/wallpaper-homestead.jpg") # 원하는 파일의 주소를 통해 파일을 저장
   storeAddress("QmPJLNMiAp3QoCKdGAgzshNRbQiZ8wQmyjYkcUHJZzQ19u") # 해당 파일의 IPFS 주소 저장
   fetchContent() # IPFS 상의 파일 주소 return => 접속.
   ```

8. IPFS 를 통해 https://ethereum.org/images/wallpaper-homestead.jpg 의 사진이 저장되었고, 해당 사진을 보여준다.

9. 계속해서 저장해 나갈 수 있다.

   ```js
   storeContent("https://upload.wikimedia.org/wikipedia/commons/e/e4/160618_MBC_Thank_You_Festival_%E2%80%93_Red_Velvet.jpg")
   storeAddress("QmbWQBKEaVBBc42sVbCUwJc294awGeb17aUEf6ijXxcVTk")
   fetchContent()
   ```

10. 추가 demo 영상 : https://www.youtube.com/watch?v=8CMxDNuuAiQ

## Authors
Translated, edited, and written by
- 강동주 [@djohnkang]['https://github.com/djohnkang']
- 유태영 [@neovansoarer]['https://github.com/neovansoarer']
- 오창희 [@5chang2]['https://github.com/5chang2']

## Acknowledgement
- Siraj Raval [llSourcell]['https://github.com/llSourcell']
  - https://github.com/llSourcell/IPFS_Ethereum_Storage
- [theschool.ai]['http://theschool.ai']

## License
Under the [MIT License][https://opensource.org/licenses/MIT]
