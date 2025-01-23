## IOCP

: 소켓이나 파일의 입출력을 **최소한의 스레드를 사용해서 처리**하는 기법

→ Window 환경에서 작동하는 제일 흔히 쓰이는 **논 블로킹** 프로세스이다.

→ 비동기 + 스레드 풀링 + 논 블로킹 + 중첩 입출력과 같은 개념들을 이용해서 작동한다.

### 스레드 풀링

: **한번 생성한 스레드를 재활용해서 시스템의 부하를 덜어주기 위한 기법**

스레드의 생성과 소멸에 소모되는 리소스가 상당하기 때문에 등장하였다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbTjn2G%2FbtsDUVZSBTE%2FEiNh0pmkDpfyb2TczjUvVk%2Fimg.png)

1. 여러 개의 스레드 **생성**
2. 실행해야 할 일이 등록될 때마다 미리 생성해 놓은 스레드 중에 할당
3. 할당된 일이 끝나면 스레드를 **소멸시키지 않고 보관**한다.

❗스레드 풀 안에 스레드가 있는 것

---

### 블로킹 vs 논 블로킹

: 처리되어야 하는 작업이 **전체적인 작업의 흐름을 막는지**가 가장 큰 차이점이다.

- 블로킹 : 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면 다른 작업이 끝날 때까지 기다렸다가 자신의 작업을 시작하는 것 <br>
→ 동기적으로 실행
- 논 블로킹 : 다른 주체의 작업에 상관없이 자신의 작업을 진행하는 것 <br>
→ 비동기적으로 실행

<div style="display: flex; justify-content: center; align-items: center; gap: 20px;">
  <div style="text-align: center;">
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FoUK3A%2FbtrJRDNBTZg%2FqzRCNnoeG86JO44Bwb5KV0%2Fimg.png" style="width: 300px; height: auto;" />
    <p>블로킹</p>
  </div>
  <div style="text-align: center;">
    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F2hc4F%2FbtrJO121k2x%2FTft1EPSRKAvPKzUQI1D83K%2Fimg.png" style="width: 300px; height: auto;" />
    <p>논블로킹</p>
  </div>
</div>


> ⭐
**동기와 비동기 / 블로킹과 논블로킹 차이점** <br>
: 각각 비슷한 개념을 다루지만 **관점이 다르다.** <br>
>- 동기/비동기 : 요청한 작업에 대해 완료 여부를 신경 써서 **작업을 순차적으로 수행**할지 아닌지에 대한 관점 <br>
    - 동기 : 요청을 보내고 응답이 올 때까지 다른 작업을 수행 X <br>
    - 비동기 : 요청을 보내고 응답을 기다리지 않고 다른 작업을 수행하고, 응답이 오면 처리하는 방식
>- 블로킹/논블록킹 : **현재 작업이 block(차단, 대기) 되느냐 아니냐**에 따라 다른 작업을 수행할 수 있는지에 대한 관점 <br>
    - 블로킹 : 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면 다른 작업이 끝날 때까지 기다렸다가 작업을 진행 <br>
    - 논 블로킹 : 다른 주체의 작업에 상관없이 자신의 작업을 진행


---

### 중첩 입출력 (Overlapped IO)

: I/O 처리 요청을 **스레드가 Device Driver에게 위임**하고 I/O 작업이 종료될 때까지 **해당 스레드는 다른 작업을 수행**할 수 있도록 하는 기법

1. I/O 작업을 요청한다.
2. WSASend함수를 호출하고 바로 return된다.


    >💡 WSASend : 데이터를 전송하는 역할로 Overlapped IO 구조체를 파라미터로 전달 <br>
    → 이 구조체에 이벤트 커널 오브젝트를 등록한 후 등록된 객체의 상태를 변경해 I/O 완료 여부를 전달할 수 있다. <br>
    → 스레드는 WSAGetOverlappedResult 함수를 통해 이 상태, I/O 완료 및 결과를 확인할 수 있다.

3. 비동기적으로 I/O 작업을 요청했다는 정보를 Device Driver에게 전송한다. <br>
→ I/O 작업을 Device Driver에게 전송한 스레드는 더 이상 I/O 작업을 신경 쓰지 않는다. <br>
→ 비동기적으로 수행
1. Device Driver는 I/O 작업이 끝날 때까지 감시하고 작업이 끝나면 끝났음을 통보한다.

> ❗ Device Driver : 입출력장치(블루투스 키보드, 유선 모니터, 마우스 등)와 컴퓨터(OS)사이에서 명령어나 데이터를 전달해주는 역할

### IOCP 동작 과정

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fba2Q0B%2FbtsDT3jPZS3%2FVns46f61vs1280UnteQz2K%2Fimg.png)

1. I/O 객체와 Completion Port 객체 연결
    
    : **I/O 작업의 완료 알림을 받기 위해** I/O 객체(네트워크 소켓 또는 파일)을 생성하고 I/O 객체를 Completion Port와 연결
    
    > 💡 Completion Port : I/O 작업의 완료 알림을 받을 수 있는 커널 오브젝트

    
2. 작업 스레드 생성과 비동기 작업 시작(WSARecv() 함수 등)
: 스레드 풀을 사용해 작업자 스레드를 생성 <br>
→ 후에 작업자 스레드는 Completion Port에서 알림을 받아 작업을 처리한다.
1. I/O 객체(디바이스)가 비동기 I/O 완료 
    
    : IOCP Queue에 완료된 비동기 I/O 내용이 저장
    
2. 작업자 스레드 대기
: 작업자 스레드는 GetQueuedCompletionStatus 함수를 사용해 CompletionPort의 알림(비동기 I/O 작업 완료 여부)을 기다린다.
1. I/O 작업 완료 알림 처리


> 💡 프로세스는 **유저모드와 커널모드를 번갈아가면서 실행**
> - 유저모드 : 제한적 사용, 프로그램의 자원에 침범X
> - 커널모드 : 모든 자원에 접근, 명령 가능 <br>
> [유저모드와 커널모드](https://blockdmask.tistory.com/69)



### IOCP 장단점

- 장점
    - 스레드 풀(Thread Pool)을 쉽게 사용할 수 있다. (운영체제가 직접 스레드 풀링 관리)
    - 스레드를 효율적으로 사용하므로 CPU 자원소모가 줄어든다.
    - Context Switching 비용이 줄어든다.
- 단점
    - 프로그램 구현이 복잡해진다.
    - Window에서만 사용이 가능하다.
    - 하나의 I/O operation마다 버퍼 영역에 대한 page-loock/unlock이 필요하다.
        - 이를 회피하기 위한 zero-byte, zero-byte recv
    - 하나의 I/O operation마다 시스템 콜이 발생한다.

---

## 한줄 정리

IOCP는 소켓이나 파일의 입출력을 **최소한의 스레드를 사용해서 처리**하는 기법으로 비동기 + 스레드 풀링 + 논 블로킹 + 중첩 입출력과 같은 개념들을 이용해서 작동한다.

1. 가장 먼저 **I/O 작업의 완료 알림을 받기 위해** I/O 객체와 Completion Port 객체를 연결한다. 
2. 그 후 스레드 풀을 사용해 작업 스레드 생성과 동시에 I/O 객체의 비동기 작업을 시작한다.  
3. I/O 객체가 비동기 작업을 완료하면 IOCP Queue에 완료된 작업 내용이 저장된다. 
4. 작업자 스레드는  GetQueuedCompletionStatus 함수를 사용해 CompletionPort의 알림을 기다리고 이후 I/O 작업을 완료한다.

<br><br>

# 브라우저에 **URL을 입력했을 때 일어나는 과정**

1. **브라우저는 입력된 url의 IP주소를 찾기 위해 DNS 캐시를 탐색한다.**
    
    이때 DNS란 도메인 이름 시스템을 의미하며 도메인 이름과 IP주소를 서로 변환하는 역할을 한다.
    
    DNS 서버에 요청하기 전에 캐싱된 DNS 기록을 먼저 확인하며, 해당 도메인 이름에 대응하는 IP 주소가 있으면 DNS 서버에 요청하지 않고 캐싱된 IP 주소를 반환한다.
    
    만약 캐싱된 DNS 기록에 없다면 DNS 서버에 요청을 보낸다.
    
2. **DNS  서버에서 해당 도메인 이름에 해당하는 IP 주소를 찾아 사용자가 입력한 URL 정보와 함께 전달한다.**
Internet service provider (ISP)를 통해 사용자와 가장 가까운 DNS 서버에 요청을 보내며 이 DNS 서버에서는 DNS query를 통해 DNS 서버들을 탐색하며 원하는 IP 주소를 찾는다.
    
    이후 IP주소와 함께 사용자가 입력한 URL 정보를 함께 전달한다.
    
3. **브라우저가 서버와 TCP 연결을 시작한다.**
    
    브라우저가 IP주소를 받으면 해당 IP 주소와 일치하는 서버와 TCP 연결로 정보를 전송한다.
    
    이때 TCP 연결이 사용되며 3-way-handshake 과정을 거쳐 연결을 생성한다.
    
4. **브라우저가 웹서버에 HTTP 요청을 전송한다.**
    
    브라우저는 GET 요청을 통해 서버에 웹 페이지를 요구한다.
    
5. **서버는 요청을 처리하고 응답을 보낸다.**
    
    요청의 헤더, 쿠키 등의 정보를 읽어서 요청 내용을 확인 및 처리하고 특정 포맷 (json, xml, html)으로 웹페이지와 상태 코드, 압축 유형 등을 담은 응답을 생성해 전송한다.
    
6. **브라우저에 HTML 콘텐츠가 표시됩니다.**
    
    렌더링 과정을 통해 브라우저에 HTML 콘텐츠가 표시된다.
    

### 계층

1. **7계층(응용 계층)** : 사용자가 주소를 입력하고, 브라우저가 HTTP나 HTTPS 프로토콜을 사용해 요청을 준비한다.
2. **6계층(프레젠테이션 계층)** : 데이터 암호화가 이루어진다.
    
    → HTTPS를 통해 SSL/TLS 암호화를 적용해 보안이 보장된다.
    
3. **5계층(세션 계층)** :클라이언트와 Google 서버 간의 세션을 생성하고 유지한다.
4. **4계층(전송 계층)** : TCP 프로토콜이 사용되어 데이터를 작은 패킷으로 분할하고, 신뢰성 있는 전송을 보장한다.
5. **3계층(네트워크 계층)** : IP 주소를 사용해 패킷이 Google 서버로 라우팅된다. DNS를 통해 www.google.com의 IP 주소를 얻어온다.
6. **2계층(데이터 링크 계층)** : MAC 주소를 사용하여 네트워크 장치 간에 데이터를 전달한다.
7. **1계층(물리 계층)** : 0과 1의 데이터가 전기적 신호, 광신호 또는 무선 신호로 변환되어 실제 네트워크를 통해 전송된다.