# TCP의 3-way Handshaking과 4-way Handshaking의 차이를 설명해주세요.

## 3-way Handshaking(연결 설정)
<img src="https://velog.velcdn.com/images/alkwen0996/post/7553f1db-dbde-4a7c-8f39-0811824b7f98/image.png" width="50%">

1. 클라이언트가 서버에 연결 요청을 보냄(SYN) -> 클라이언트는 SYN_SENT 상태(연결 요청 상태)
2. 서버는 요청 수락과 응답을 보냄(SYN + ACK) -> 서버는 SYN_RECEIVED 상태(연결 요청 수락 상태)
3. 클라이언트는 응답을 받고 연결 수립(ACK)-> 클라이언트는 ESTABLISHED 상태(연결 수립 상태)

## 4-way Handshaking(연결 해제)
<img src="https://velog.velcdn.com/images/alkwen0996/post/60cde43c-e4a0-4be0-a5ac-5727d92e3591/image.png" width="50%">

1. 클라이언트가 서버에 연결 종료 요청을 보냄(FIN) -> 클라이언트는 FIN_WAIT_1 상태(연결 종료 요청 상태)
2. 서버는 아직 보낼 데이터가 남아있을 수 있기 때문에 요청 수락 음답을 보냄(ACK) -> 서버는 CLOSE_WAIT 상태(연결 종료 요청 수락 상태)
3. 종료할 준비가 되었다는 신호를 보냄(FIN) -> 클라이언트는 서버로부터 데이터가 올 수 있으므로 FIN을 받고 대기하는 TIME_WAIT 상태로 변경
4. 클라이언트는 응답을 받고 연결 종료(ACK) -> 클라이언트는 CLOSED 상태(연결 종료 상태)

> 만약 3-way Handshaking 중 ACK가 누락된다면 어떤 일이 발생하나요?
>- 클라이언트는 서버로부터 ACK를 받지 못하고 타임아웃이 발생하면 다시 SYN을 보냄.
>- 서버는 클라이언트로부터 ACK을 받지 못하면 SYN+ACK를 계속 보냄. 클라이언트는 중복된 데이터를 받게 됨.
  >- 이러한 상황을 방지하기 위해 시퀀스 번호를 사용하여 중복된 데이터를 거르는 방법을 사용함.

> 플래그 비트
> - SYN : 연결 설정 요청
>
> Sequence Number를 랜덤으로 설정하여 통신 상대방에게 전달
> - ACK : 응답 확인
> 
> Sequence Number에 1을 더한 값을 ACK 번호로 설정하여 상대방에게 전달
> - FIN : 연결 종료 요청

# TCP의 흐름제어 기법 중 슬라이딩 윈도우 방식은 무엇인가요?
## 흐름제어 기법
- 송신측과 수신측의 데이터 처리 속도를 제어하는 것


## stop-and-wait 방식
<img src="https://afteracademy.com/images/what-is-stop-and-wait-protocol-example-03e731cda541ab63.jpg" width="50%">

- 데이터를 전송하고 수신 확인(ACK)을 받은 후 다음 데이터를 전송하는 방식

## 슬라이딩 윈도우 방식
<img src="https://blog.skby.net/wp-content/uploads/2018/11/2-47.png" width="50%">

- 일일이 수신 확인을 받지 않고 수신측에서 감당할 수 있는 윈도우 크기를 설정하여 송신측에서는 윈도우 크기만큼 패킷을 보내고 수신 확인을 받음.
> 슬라이딩 윈도우에서 윈도우 크기가 커지면 어떤 장단점이 있을까요?
> ### 장점
> - 일일이 수신 확인을 받지 않고 윈도우 크기만큼 보내고 수신 확인을 받기 때문에 보다 빠른 전송이 가능

> ### 단점
> - 윈도우 크기가 커질수록 수신측에서 처리할 데이터가 많아지기 때문에 오버플로우가 발생하여 데이터 손실이 발생할 수 있음.
