# SSL (Secure Scokets Layer)

응용 계층과 전송 계층 사이의 **독립적인 보안 계층**에서 보안을 제공하는 프로토콜

→ 클라이언트와 서버가 통신할 때 제 3자가 메시지를 도청하거나 변조하지 못하도록 한다. 

→ **보안 세션**을 기반으로 데이터를 암호화한다.

→ 보안 세션이 만들어질 때 **인증 메커니즘, 키 교환 암호화 알고리즘, 해싱 알고리즘이 사용**된다.

+) SSL(Secure Socket Layer)에서 버전이 올라가며 TLS(Transport Layer Security)로 명칭이 바뀌었으나 같이 부른다.

### 보안 세션

: 보안이 시작되고 끝나는 동안 유지되는 세션

→ **핸드셰이크**를 통해 생성되며 보안 세션을 기반으로 상태 정보 등을 공유한다.

## **TCP의 3-way Handshaking**

TCP/IP프로토콜을 이용해서 통신을 하는 응용프로그램이 데이터를 전송하기 전에 먼저 정확한 전송을 보장하기 위해 **수신측과 사전에 세션을 수립하는 과정**

→ 이 과정을 통해 클라이언트와 서버 모두 **데이터를 전송할 준비가 되었다는 것이 보장**된다.

### 과정

![](https://github.com/BakHyegyeong/cs_study/raw/main/Network/NT_02/image.png)

1. 클라이언트가 서버에 접속을 요청하는 **syn 패킷을 전송**하고 **syn_sent 상태**가 되어 ack응답을 기다린다.

2. 서버는 **syn요청을 받고** 클라이언트에게 요청을 수락한다는 **ack와 syn flag**가 설정된 패킷을 발송하고 **syn_received**상태가 되어 ack 응답을 기다린다.

3. 클라이언트가 **syn+ack를 수신**한 후 서버에 다시 **ack를 보내면** 세션이 생성되고 이때부터 데이터가 전송된다.
    
    → 이때 서버와 클라이언트는 established상태가 된다.
    

- client : syn 전송 → syn_sent → syn+ack 수신 → 다시 ack 전송 → established
- server : syn 수신 → syn+ack 송신 → syn_received → ack 수신 → established


> ⭐
> 
> - SYN: Synchronize Sequence Numbers  
>   - TCP 헤더에서 플래그 비트(flag bit)의 SYN에 동기화 시퀀스 번호가 저장된다.  
>   - 양쪽이 보낸 **최초의 패킷에만 이 플래그가 설정**된다.  
>   - 직전에 보낸 ACK의 값으로 설정된다.  
>
> - ACK: Acknowledgment  
>   - TCP 헤더에서 플래그 비트(flag bit)의 ACK에 **직전 패킷의 시퀀스 넘버가 담겨** 패킷이 제대로 도착했는지 확인할 수 있다.  
>   - 클라이언트가 보낸 최초의 SYN 패킷 이후에 전송되는 모든 패킷은 이 플래그가 설정되어야 한다.  


## TLS의 Handshake

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F990B63335BC96D3020)

1. **ClientHello** : 사용할 SSL or TLS 버전과 **사이버수트,** **임의의 숫자로 된 난수**를 함께 전송한다.
    
    > 💡 사이버수트 :  암호화 알고리즘의 집합으로써 프로토콜, AEAD 사이퍼 모드, 해싱 알고리즘이 나열된 규약을 의미한다. <br>
    → 클라이언트가 사이버수트를 전달하면 **서버가 사용할 암호화 알고리즘을 결정한다.**
    > - AEAD 사이퍼 모드 : 데이터 암호화 알고리즘
    
    

2. **ServerHello** : 서버는 **사이버 수트에서 사용할 암호화 알고리즘을 선택한 후 임의의 숫자로 된 난수**를 함께 전송한다.
    
    **+) Certificate** : **공개키가 포함된 SSL 인증서**를 전송한다. <Br> 
    → 인증서 내부에 공개키가 없다면 서버가 클라이언트에 직접 공개키를 전달한다.
    
3. 인증서 확인 및 pre-master-secret 암호화  
    - CA의 공개키로 SSL 인증서를 복호화한다.
        
        → 복호화가 된다면 서버가 보내온 SSL 인증서가 CA에서 발급한 인증서임이 보장된다.
        
    - 클라이언트는 **자신의 난수와 서버의 난수를 이용해 pre-master-secret를 생성한 후 인증서를 통해 얻은 공개키로 이를 암호화**한다.

4. 암호화된 pre-master-secret를 서버에 전송한다.
5. **pre-master-secret 복호화** : 서버는 서버의 비밀 키를 이용해 복호화한다.
    
    → 이를 master-secret로 저장한다.
    
6.  **master-secret 및 session key 생성** : master-secret로 session Key를 생성하고 추후 암호화 통신에 사용한다.
    
    → 이 session key는 대칭키 암호화에 사용된다.
    


> ⭐
> - 클라이언트가 인증서의 공개키로 pre master secret 암호화하고 서버에서 이를 개인키로 복호화는 과정 - 비대칭키 방식
> - session key를 활용한 암호화 방식의 통신 - 대칭키 방식

### TCP 3-way-handshake와 TLS Handshake

- TCP 3-way-handshake : TCP 기반의 프로토콜을 통해 **연결을 확립하는 과정**
- TLS Handshake : 클라이언트와 서버 간에 **암호화 통신을 시작하기 위한 정보 교환 과정**

**TCP 3-way-handshake가 먼저 일어난 후 TLS Handshake가 이루어지는 것이다.**

## SSL과 TLS

### 공통점

- SSL과 TLS 모두 서버, 애플리케이션, 사용자 및 시스템 간의 **데이터를 암호화하는 통신 프로토콜**이다.
- **Handshake을 통해 세션을 수립**하고 브라우저와 웹 서버 간에 **암호화된 통신을 설정하는 디지털 인증서**를 사용한다.

### 차이점

| 구분 | SSL (Secure Sockets Layer) | TLS (Transport Layer Security) |
| --- | --- | --- |
| 버전 | SSL 1.0, SSL 2.0, SSL 3.0 | TLS 1.0 (SSL 3.1), TLS 1.1 (SSL 3.2), TLS 1.2, TLS 1.3 |
| 보안성 | 초기 버전(특히 SSL 2.0)은 취약점이 많아 보안이 약함 | SSL보다 개선된 암호화 알고리즘과 보안 기능을 제공하여 보안성이 더 높음 |
| 암호화 알고리즘 | 상대적으로 제한적 | 더 다양하고 강력한 암호화 알고리즘을 지원 |
| 핸드셰이크 프로세스 | 비교적 단순 | 보다 세부적이고 안전한 핸드셰이크 과정을 제공 |
| 포트 번호 | 기본적으로 443 포트 사용 | 기본적으로 443 포트 사용 |
| 호환성 | 오래된 시스템과의 호환성 문제가 있을 수 있음 | 최신 프로토콜이지만, 이전 버전과 호환을 지향함 |
| 알림 메시지 | SSL에는 두 가지 유형의 알림 메시지만 있으며 알림 메시지는 암호화되지 않는다. | TLS 알림 메시지는 암호화되며 더 다양하다. |

# OSI 7계층과 TCP/IP 4계층

## OSI 7계층

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbqbCLk%2Fbtryyf3MHwI%2FE20STGOkRn5Y1Lkc88oDP1%2Fimg.png)

1. **물리계층(Physical Layer)** : 전기적, 기계적, 기능적인 특성을 이용해 통신 케이블로 데이터를 전송하는 계층
    - 데이터가 무엇인지, 어떤 오류가 있는지 신경쓰지 않고 데이터만 전달한다.
    - 통신 단위 : 비트(Bit)
    - 예시 : 통신 케이블, 리피터, 허브 등
2. **데이터 링크 계층(DataLink Layer)** : 물리계층을 통해 송수신되는 정보의 오류와 흐름을 관리하여 안전하게 정보를 전달할 수 있도록  도와주는 계층 
    - 통신 오류를 검출하고, 재전송하는 기능
    - 맥 주소(물리적 주소)를 가지고 통신한다.
    - 통신 단위 : 프레임(Frame)
    - 예시 : 브릿지와 스위치
3. **네트워크 계층(Network Layer)** : 데이터의 경로를 선택해 주소를 정하고, 가장 안전하고 빠르게 패킷을 전달하는 계층
    - 라우터(Router)를 통해 경로를 선택하고 주소(IP)를 정하고 경로(Route)에 따라 패킷을 전달한다.
    - 전송 단위 : 패킷(Packet)
    - 예시 : 라우터
4. **전송 계층(Transport Layer)** : 연결을 기반으로 두 지점간의 신뢰성 있는 데이터를 주고 받게 해주는 계층
    - 포트 번호와 전송 방식을 결정합니다.
    - 예시 : TCP와 UDP
5. **세션 계층(Session Layer) :** 데이터가 통신하기 위해 논리적으로 연결
    - 두 지점간의 프로세스 및 통신하는 호스트 간의 연결 유지
    - TCP/IP 세션을 만들고 없애는 역할
    - 예시 : API, Socket
6. **표현 계층(Presentation Layer)** : 데이터 표현이 다른 응용 프로세스간의 데이터 표현방식을 결정하는 계층
    - 파일인코딩, 명령어를 포장, 압축, 암호화
        
        → 독립성과 암호화를 제공
        
    - 예시 : JPEG, MPEG, GIF, ASCII
7. **응용 계층(Application Layer)** : 응용 프로세스와 직접 관계하여 일반적인 응용 서비스를 수행하는 계층
    - 예시 : HTTP, FTP, SMTP, POP3, IMAP, Telnet 등과 같은 프로토콜

## **TCP/IP 4계층**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fzcplw%2Fbtryx3PWhzI%2FTwhREYTPDOvs0HilLDkrj1%2Fimg.webp)

1. **네트워크 액세스 계층(Network Access Layer)** : 물리적인 데이터의 전송을 담당하는 계층
    - OSI 7계층의 **물리계층(1)과 데이터 링크 계층(2)**
    - TCP/IP 패킷을 네트워크 매체로 전달하는 것과 네트워크 매체에서 TCP/IP 패킷을 받아들이는 과정을 담당
    - 같은 네트워크 안에서 데이터가 전송
    - 기본적인 에러 검출과 패킷의 프레임화를 담당
    - 데이터 단위 : 프레임(Frame)
    - 전송 주소 : 맥 주소(물리적 주소)
    - 예시 : MAC, LAN, 패킷망 등에 사용되는 것(대표적으로 **Ethernet**)
2. **인터넷 계층(Internet Layer)** : 네트워크상 최종 목적지까지 정확하게 연결되도록 **연결성을 제공**하는 계층
    - OSI 7계층의 네트워크 계층(3)에 해당
    - 통신 노드간의 IP패킷을 전송하는 기능과 라우팅 기능을 제공한다.
    - 데이터 단위 : 패킷(Packet)
    - 전송 주소 : IP
    - 예시 : IP, ARP, RARP
3. **전송 계층(Transport Layer)** : 통신 노드 간의 연결을 제어하고, **신뢰성** 있는 데이터 전송을 담당하는 계층
    - OSI 7계층의 전송 계층(4)에 해당
    - **IP와 Port**를 이용하여 프로세스와 통신
    - 데이터 단위 : 세그먼트(Segment)
    - 전송 주소 : Port
    - 예시 : TCP와 UDP
4. **응용 계층(Application Layer)** : 사용자와 가장 가까운 계층으로 사용자-소프트웨어 간 소통을 담당하는 계층
    - OSI 7계층의 **세션 계층(5), 표현 계층(6), 응용 계층(7)**에 해당한다.
    - 애플리케이션들이 데이터를 교환하기 위해 사용하는 프로토콜을 정의
    - 데이터 단위 : **데이터(Data) / 메세지(Message)**
    - 예시 : 파일 전송, 이메일, FTP, **HTTP** , DNS, SMTP 등

| TCP/IP 4계층 | 역할 | 데이터 단위 | 전송 주소 | 예시 | 장비 |
| --- | --- | --- | --- | --- | --- |
| 응용 계층 (Application Layer) | 응용프로그램끼리의 데이터 송수신 | Data/Message | - | 파일 전송, 이메일, FTP, HTTP, DNS, SMTP 등 | - |
| 전송 계층 (Transport Layer) | 호스트끼리 송수신 | Segment | Port | TCP, UDP 등 | 게이트웨이 |
| 인터넷 계층 (Internet Layer) | 데이터 전송을 위한 논리적 주소 및 경로 지정 | Packet | IP | IP, ARP, ICMP, RARP 등 | 라우터 |
| 네트워크 연결 계층 (Network Acees Layer) | 실제 데이터인 프레임 송수신 | Frame | MAC | Ethernet 등 | 브릿지, 스위치 |
