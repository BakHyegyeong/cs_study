# WebSocket이란 무엇인가요?
## websocket 이전 통신 방식
### Polling
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdD5Sry%2FbtrG6Xhv3kQ%2FunJPhTb24eySmpab9OviCk%2Fimg.png">

- 클라이언트가 서버에게 주기적으로 요청을 보내고 서버는 클라이언트의 요청에 즉시 응답하는 방식
- 응답에 대한 데이터가 없을 때도 응답을 보내야 하기 때문에 불필요한 트래픽이 발생하여 서버 부하 발생

### Long Polling
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzxnyF%2FbtrG8r3vJmV%2FB10zbZkayqRkNZnnlF50I1%2Fimg.png">

- 클라이언트가 서버에게 요청을 보내고 서버는 클라이언트의 요청에 대한 응답을 바로 보내지 않고 특정 이벤트나 타임아웃이 발생할 때까지 대기하는 방식.
- 이전 Polling 방식보다는 서버 부하가 줄어들지만, 여전히 타임 아웃으로 인한 불필요한 트래픽이 발생

## WebSocket
- HTML5에서 등장한 기술로, 클라이언트와 서버 간의 지속적인 양방향 통신을 가능하게 하는 기술
- 한번 연결을 맺으면 계속 연결을 유지하며, 서버나 클라이언트가 원할 때 데이터를 보낼 수 있음
- HTTP 프로토콜을 사용하여 TCP handshake를 통해 연결을 맺고, 이후에는 TCP 프로토콜을 사용하여 데이터를 주고받음
- 연결 맺고 끊는 과정이 없기 때문에 다른 방식보다 네트워크 오버헤드 감소

### HTTP와 WebSocket의 차이
<img src="https://velog.velcdn.com/images/top1506/post/cc278fca-44a2-4a9e-a9d3-175dd4613cf5/image.png">
- HTTP는 웹소켓과 달리 매번 연결을 맺고 끊는 과정을 거침(요청-응답 모델)
- 비연결성으로 단반향 통신만 가능하다. (클라이언트가 서버에게 요청, 서버는 응답만 가능). 반면, 웹소켓은 양방향 통신이 가능하다.

### 소켓이란? 
<img src="http://jkkang.net/unix/netprg/chap2/xtrsq001.gif">

- 다른 컴퓨터와 통신하기 위한 통로
- TCP/IP 프로토콜 위에 소켓이 존재하며, 소켓을 통해 데이터를 주고받음
