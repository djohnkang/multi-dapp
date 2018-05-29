# Chat app

## Web 3.0 Tech Stack

* 지금까지는 EVM 파트에 집중했다면, Whisper 파트를 간단하게 살펴보자.

| -              | Etherum                       | -               |
| -------------- | ----------------------------- | --------------- |
| -              | Mist Browser                  | -               |
| -              | Dapp                          |                 |
| Swarm (Strage) | **Whisper (Messaging)**       | EVM (Consensus) |
| -              | Hardware Clients (Node, Peer) | -               |
| -              | Internet                      | -               |



## Concept

* whisper protocol? : dApp 들 간의 통신 protocol.
*  Dynamic communication between peers.
*  블록 체인과 관계 없음! peer to peer communication protocol, Out of Block!
* A가 K에게 메세지를 보내는 시나리오.
  * 일단 해당 메세지를 decrypt 할 수 있는 key 는 K만이 가지고 있음.
  * 모든 메세지는 우선 주변 peer 에 전파! (B, C, D, E.. )
  * 계속해서 주변 peer에게 전파. (F, G, H, I, J..)
  * K 까지 도달.
  * K가 수신해도, 계속해서 주변 peer들에게 전파. (L, M, N, O, P..)
  * K 를 제외한 모든 peer들이 해당 메세지가 자신에게 온 것이 아님을 알지만 수신-전파에 참여. Why?
    * **Darkness** 라는 특성.
    * 이론적으로 메세지를 보냈을 때, 절대 추적 불가능
    * 특정 peer에게 도달했다고 하더라도, 모든 peer 들이 받게 되므로, 해당 peer 가 수신자인지 증명할 수 없음.
    * 이런 Darkness 라는 특성을 위해 Bandwidth(Data 크기) 와 Latency(메세지 도달까지 걸리는 시간) 를 희생
      * 발신자 조차 수신자에게 어떤 전달 경로를 통해 메시지가 도착할 것인지도 알 수없음.
      * 언제 목표한 수신자가 받아보게 될지도 알 수 없음.
      * TTL(time to live) 가 있기때문에 특정 시간이 지나면 사라짐.
* https://github.com/ethereum/wiki/wiki/Whisper-Overview

## Instructions

* 현제 repo 를 다운로드 한다.

* 해당 폴더의 이름을 `whisper_chat_1` 로 바꾼다.

* `whisper_chat_1\src\WhisperChat.vue` 의 코드중 해당 코드를 찾아 바꾼다.

  ```js
  ...
  methods: {
      sendMessage() {
          ...
          let postData = {
  				ttl: 7,
  				topic: '0x07678231', // Whisper 는 topic 을 바탕으로 messgae 를 받는다.
  				powTarget: 2.01,
  				powTime: 100,
  				payload: encodeToHex(JSON.stringify(msg)),
  			};
  		...
      },

  	...

      configWithKey() {
          ...
          let filter = {
              topics: ['0x07678231'] // <= topics 를 위의 postData 의 topic 과 같게 한다.
          }
          ...
      },
  };
  ...
  ```

* `whisper_chat_1` 를 복사하여 `whisper_chat_2` 로 같은 위치에 만든다.

* `whisper_chat_1\webpack.config.js`

  ```js
  ...

  devServer: {
      histroyApiFallback: true,
      noInfo: true,
      port: 8080
  },

  ...
  ```

* `whisper_chat_2\webpack.config.js`

  ```js
  ...

  devServer: {
      histroyApiFallback: true,
      noInfo: true,
      port: 8081
  },

  ...
  ```

* 3개의 터미널을 켠다.

  * `~>` => `$ geth --shh --rpc --rpccorsdomain '*'`
    * http://localhost:8545 로 접속.
  * `...\whisper_chat_1>`  => `$ npm install` => `$ npm run dev` => http://localhost:8080 접속
  * `...\whisper_chat_2>`  => `$ npm install` => `$ npm run dev` => http://localhost:8081 접속

* 두개의 창을 함께 띄우고, 각각 username 을 다르게 입력 => Start.

  * http://localhost:8080 의 My public key 를 http://localhost:8081 의 Recipient's pulic key 에,
  * http://localhost:8081 의 My public key 를 http://localhost:8080 의 Recipient's pulic key 에, 넣는다.
  * **반드시 입력한 Recipient's public key 의 마지막에 띄어쓰기가 없어야 한다!**
  * message 를 보낸다! (아주 아주 긴 message 는 보내지지 않음 => Bandwidth limit.)

## Authors
Translated, edited, and written by
- 강동주 [@djohnkang]['https://github.com/djohnkang']
- 유태영 [@neovansoarer]['https://github.com/neovansoarer']
- 오창희 [@5chang2]['https://github.com/5chang2']

## Sources
- Siraj Raval [llSourcell]['https://github.com/llSourcell']
  - https://github.com/llSourcell/Decentralized_Chat
- [theschool.ai]['http://theschool.ai']

## License
Under the [MIT License][https://opensource.org/licenses/MIT]
