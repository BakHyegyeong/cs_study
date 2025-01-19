# TCP와 UDP 패킷구조

## TCP 패킷 헤더 구조

![](https://nesoy.github.io/assets/posts/20181010/1.png)

- 발신지와 수신지의 포트 주소
- 시퀀스 번호 : 송신자가 지정하는 순서 번호
- 응답 번호 (ACK number) : 수신한 바이트의 수를 응답하기 위해 사용
- 데이터 오프셋 : 데이터의 시작 위치를 표현 (TCP 헤더의 크기)
- 제어 비트 : SYN, ACK, FIN 등의 제어 번호
- 윈도우 크기 : 수신 윈도우의 버퍼 크기를 지정
- 체크섬 : 데이터에 대한 오류 검출

## UDP 패킷 헤더 구조

- 발신지와 수신지의 포트 주소
- 데이터의 길이 : UDP 헤더와 데이터의 총 길이
- 체크섬 : 데이터에 대한 오류 검출

## TCP와 UDP 헤더 공통점과 차이점

- 공통점
    - 발신지와 수신지의 포트 주소
    - 체크섬

- 차이점 : TCP는 UDP와 달리 아래 정보가 추가로 담겨진다.
    - 시퀀스 번호
    - **응답 번호** : ACK값이 연속적인지 확인해 정상적인 통신이 이루어지고 있는지 확인한다.
        
        → 신뢰성과 데이터 순서 보장
        
    - 데이터 오프셋
    - **윈도우 크기** : 수신자가 윈도우 크키값을 통해 수신량을 정할 수 있다.
        
        → 흐름 제어
        

→ TCP는 **신뢰성, 데이터 순서 보장, 흐름제어** 등을 지원하기 위해 추가적인 필드를 가지고 있어서 헤더가 복잡하다.

---

# **HTTP Keep-Alive와 TCP Keep-Alive**

## Keep Alive

Single Connection으로 여러 request, response를 주고 받을 수 있게끔 하는 persistent connection을 만드는 기능


> 💡 **persistent connection** <br>
> 통신의 주체인 서버와 클라이언트 중 하나가 명시적으로 connection을 close하고나 정책적으로 close될 때까지 **계속해서 connection을 유지**하는 경우


## TCP Keep Alive

TCP 계층에서 연결을 확인하기 위해 **주기적으로 ACK를 주고 받는 행위**

→ ACK를 정상적으로 받지 못한다면 **OS에서** TCP 연결을 종료한다.

→ TCP의 KeepAlive는 OS의 설정에 의해서 관리된다.

```java
net.ipv4.tcp_keepalive_time=200
net.ipv4.tcp_keepalive_intvl=200
net.ipv4.tcp_keepalive_probes=5
```

## HTTP Keep Alive

HTTP 계층에서 **HTTP Connection을 유지하기 위한 메커니즘**

→ Keep-Alive 시간 동안 HTTP 요청을 받지 못한다면 **Web Server에 의해** TCP Connection이 종료된다.

→ Keep-Alive는 Header를 통해서 Client와 Server가 주고 받으며 규약된다.

```java
HTTP/1.1 200 OK
Connection: Keep-Alive
Keep-Alive: timeout=10, max=500
```

## TCP Keep Alive와 UDP Keep Alive

TCP Keep Alive는 **connection을 지속적으로 얼마나 유지하는지**에 초점을 두고 있고 HTTP Keep Alive는 **최대 얼마 동안 connection을 유지할 것인지**에 초점을 두고 있다.