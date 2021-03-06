# Web socket: HTTP와의 비교를 중심으로

일반적으로 Apollo Client의 query와 mutation은 **HTTP**를, subscription은 **Web socket**을 사용해서 정보를 받아온다

<hr/>

_\* socket: 컴퓨터 네트워크를 경유하는 프로세스 간 통신의 종착점_

_통신연결을 보내는 쪽(Client Socket), 통신연결을 받는 쪽(Server Socket) 모두 socket이 open되어있는 상태여야 통신이 가능함_

### 👀 Web socket이란?

- HTTP를 이용한 실시간 통신의 문제를 해결하기 위해 등장
- HTTP 포트 80, 443 위에서 동작하도록 설계됨 -> HTTP 프로토콜과 호환이 된다
  - 웹소켓 핸드셰이크는 HTTP 업그레이드 헤더를 사용해 HTTP 프로토콜에서 웹소켓 프로토콜로 변경
- 통신할 때 HTTP에 비해 cost가 적게 듬(헤더 크기가 작음)

**실시간 메시징, 트랜잭션 및 애플리케이션 특성 상 트래픽이 높고 지연시간이 낮은 환경에서 유용**

> _웹 소켓은 매우 짧은 대기시간과 높은 볼륨이 필요한 경우에 매우 효과적인 대책이 된다. 실시간 양방향 데이터 통신이 필요한 경우, 많은 수의 동시 접속자를 수용해야 하는 경우, 브라우저에서 TCP 기반의 통신으로 확장해야 하는 경우 등의 상황에서 사용하면 좋다._

#### 장점

1. 양방향 통신(Duplex)
   <br />
   **HTTP**의 경우

   ```
   클라이언트 ---요청---> 서버
   클라이언트 <---응답--- 서버
   ```

   이런식으로 한 번에 한 방향으로만 통신이 이루어지지만(half-Duplex),
   <br />
   **Web socket**의 경우는

   ```
   클라이언트 <---통신---> 서버
   ```

   양방향으로 통신을 할 수 있음

1. 실시간 네트워킹

- 클라이언트와 서버가 특정한 포트를 통해서 연결을 유지하고 있어서(stateful) 실시간으로 통신이 가능함
  - 채팅이나 동영상 스트리밍같은 즉각적으로 정보를 주고받는게 필요한 경우 사용

#### 단점

1. cross browser 문제

- 일부 구형 브라우저에서 Web socket을 지원하지 않음
  - _전 세계 브라우저 96.66 % ~ 96.84 % 는 지원하는데... 큰 단점일까?🤔_

2. stateful
   HTTP와 다르게 클라이언트와 서버의 연결을 유지하기 때문에

- 부하가 발생할 수 있음
- 비정상적으로 연결이 끊어졌을 때(ex) 새로고침) 적절하게 대응할 필요가 있음

  - 구현에 좀 더 어려움을 겪을 수 있음

#### Websocket의 통신 과정

(사진)

1. 클라이언트가 서버에게 HTTP로 연결 요청
   (캡쳐 사진)
2. 서버가 클라이언트에게 HTTP로 응답
   (캡쳐 사진)
3. 이후 클라이언트가 정상적으로 응답을 받으면 핸드쉐이크 종료
   이후 웹소켓 프로토콜로 통신

서버와 클라이언트간의 연결은 HTTP 프로토콜을 통해 이루어진다.
