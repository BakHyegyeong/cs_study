## Q1. TLS Handshake 과정
1. ClientHello : 사용할 SSL or TLS 버전과 사이버수트, 임의의 숫자로 된 난수를 함께 전송한다.
2. ServerHello : 서버는 사이버 수트에서 사용할 암호화 알고리즘을 선택한 후 임의의 숫자로 된 난수를 함께 전송한다.
3. Certificate : 공개키가 포함된 SSL 인증서를 전송한다.
4. 인증서 확인 및 pre-master-secret 암호화
5. 암호화된 pre-master-secret를 서버에 전송한다.
6. pre-master-secret 복호화
7. master-secret 및 session key 생성

## Q2. TCP 3-way-handshake와 TLS Handshake
- TCP 3-way-handshake : TCP 기반의 프로토콜을 통해 연결을 확립하는 과정
- TLS Handshake : 클라이언트와 서버 간에 암호화 통신을 시작하기 위한 정보 교환 과정

→ TCP 3-way-handshake가 먼저 일어난 후 TLS Handshake가 이루어지는 것이다.

## Q3. 데이터링크 계층과 전송 계층 모두 데이터에 대한 신뢰성을 보장한다. 두 계층의 차이점은?
- 데이터링크 계층 : 네트워크 상에서 데이터가 이동할 때 여러 노드를 거치는데 각 노드들 간의 데이터 신뢰성을 제공한다. <br>
→ 이진 나눗셈 기반의 오류 제어
- 전송 계층 : 각 노드를 거쳐 엔드포인트에 데이터가 도달했을 때의 데이터의 신뢰성을 제공한다. <br>
→ 패리티 비트를 이용한 오류 제어 + TCP 프로토콜로 데이터의 흐름 및 혼잡도 제어

## Q4. 7계층에서 사용되는 프로토콜
- HTTP, HTTPS : 웹 상에서 데이털을 송수신할 때 이용
- SMTP, POP3, IMAP : 메일을 송수신할 때 사용
- FTP : 파일을 송수신할 때 사용

## Q5. 라우터가 무엇이고 어떤 계층에서 사용되는지
- 3 계층인 네트워크 계층
- IP 번호를 이용해 네트워크 상에서 데이터가 이동할 때 각 노드들 간의 경로를 찾기 위한 하드웨어 장비

## Q6. OSI 7계층의 캡슐화란?
- 송신하려는 데이터가 상위 계층에서 하위 계층을 지나면서 새로운 데이터로 거듭나는 것
- 7계층 : 데이터를 받는다.
- 4계층 : TCP 프로토콜과 Port번호를 포함한 헤더 → 세그먼트
- 3계층 : IP주소를 포함한 헤더 → 패킷
- 2계층 : MAC주소를 포함한 헤더 → 프레임

## Q7. 포트 번호는 어떤 기능을 하는지
- 엔드포인트에 데이터가 도착했을 때 어떤 프로세스로 데이터를 전달해야할지를 결정한다.

## Q8. IP주소와 MAC 주소란?
- IP주소 : 네트워크에서 데이터를 라우팅하고 식별하기 위한 가상의 주소 <br>
→ 컴퓨터와 네트워크 간의 상호 작용에서 사용된다.
- MAC 주소 : 네트워크 장치에 할당된 고유한 식별 주소 <br>
→ 하드웨어 수준에서 사용된다.

## Q9. SSL/TLS란 무엇인가요?
- SSL(Secure Sockets Layer)과 TLS(Transport Layer Security)는 인터넷 통신을 보호하는 암호화 프로토콜이다.
- TLS는 SSL의 발전된 버전으로, 웹사이트와 사용자 간의 통신을 암호화하여 기밀성을 보장하며, HTTPS의 기반이 된다.

## Q10. TLS는 어떤 방식으로 데이터의 무결성과 기밀성을 보장하나요?
TLS는 다음과 같은 방식으로 보안을 제공한다.
- 기밀성(Confidentiality): 대칭키 암호화(AES, ChaCha20)와 공개키 암호화(RSA, ECDHE)를 사용하여 데이터 보호
- 무결성(Integrity): HMAC(Hash-based Message Authentication Code)를 활용하여 데이터 변조 방지
- 인증(Authentication): X.509 인증서를 사용하여 서버 및 클라이언트를 검증

## Q11. OSI 7계층과 TCP/IP 4계층 모델의 차이점은 무엇인가요?
- OSI 모델은 7계층(물리, 데이터링크, 네트워크, 전송, 세션, 표현, 응용 계층)으로 구성되며 개념적으로 세분화되어 있다.
- TCP/IP 모델은 4계층(네트워크 인터페이스, 인터넷, 전송, 응용 계층)으로 단순화되어 실용성을 중시한다.
- TCP/IP 모델은 실제 네트워크 구현에 기반하며, OSI 모델은 이론적인 참조 모델이다.

## Q12. 전송 계층(Transport Layer)에서 TCP와 UDP의 차이점은 무엇인가요?
- TCP(Transmission Control Protocol): 연결 지향, 신뢰성 보장(3-way handshake, 흐름 제어, 오류 검출), 속도는 상대적으로 느림 (ex. HTTP, FTP)
- UDP(User Datagram Protocol): 비연결 지향, 신뢰성 없음, 빠른 전송 속도 (ex. DNS, VoIP, 스트리밍)

## Q13. TCP/IP 네트워크 계층에서 사용하는 주요 프로토콜은 무엇인가요?
- IP(Internet Protocol): 패킷을 목적지까지 라우팅
- ICMP(Internet Control Message Protocol): 네트워크 오류 및 상태 확인 (ping)
- ARP(Address Resolution Protocol): IP 주소를 MAC 주소로 변환
- RARP(Reverse ARP): MAC 주소를 IP 주소로 변환
- IGMP(Internet Group Management Protocol)**: 멀티캐스트 그룹 관리 
