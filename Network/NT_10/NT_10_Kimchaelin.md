# 리피터, 허브, 브리지, 라우터는 무엇인가요?
## 물리 계층 장비
### 리피터
- 케이블이 길수록 신호가 약해지므로 신호를 증폭시켜주는 장치

### 허브
- 리피터 역할을 하며, 기존 리피터와 달리 여러 장비를 연결할 수 있음.
- CSMA/CD 방식을 사용하여, 여러 장비에서 동시에 데이터를 전송하지 못함.

#### CSMA/CD 방식(Carrier Sense Multiple Access with Collision Detection) : 여러 장비가 동시에 데이터를 전송하려고 할 때 충돌이 발생하면 재전송을 요청하는 방식

### 리피터와 허브의 차이는 무엇인가요?
- 리피터는 단순히 신호를 증폭해주는 역할만 하지만, 허브는 여러 장비를 연결할 수 있음.

## 데이터 링크 계층 장비
### 브리지
- 브리지는 물리적으로 분리된 두 네트워크를 연결하는 장치
- 브리지는 MAC 주소를 기반으로 패킷을 전송함.

#### 제공 기능
- Leaarning : 브리지로 전송된 통신의 맥 주소를 맥 주소 테이블에 저장. 맥 주소를 기반으로 패킷을 전송함.
- Flooding : 목적지 정보가 맥 주소 테이블에 없는 주소라면 들어온 포트를 제외한 모든 포트로 전송하여 테이블에 새 주소들을 추가함.
- Forwarding : 목적지 정보가 맥 주소 테이블에 있는 주소라면 해당 포트로 데이터를 전송함. 없으면 Flooding을 수행함.
- Filtering : 같은 세그먼트 상에 있는 장비는 브리지를 건너지 못하게 함.
- Aging : 일정 기간 내에 전송된 통신이 없다면 맥 주소를 테이블에서 삭제함.

###### MAC 주소 : 네트워크 카드에 할당된 고유한 주소(컴퓨터 식별자)

### 스위치
- 브릿지와 같이 다른 네트워크끼리 연결하는 장치
- 브릿지 상위 호환 역할을 함.

#### 종류
- L2 스위치 
  - 데이터 링크 계층에서 동작, MAC 주소를 기반으로 패킷을 전달
- L3 스위치 
  - L2 스위치에 라우팅 기능 추가된 장비(라우터는 소프트웨어 방식, L3 스위치는 하드웨어 방식. L3 스위치가 더 빠름)
  - 네트워크 계층에서 동작, IP 주소를 기반으로 패킷을 전달.
- L4 스위치 
  - L3 스위치에 로드 밸런싱 기능 추가된 장비. 
  - 전송 계층에서 동작, 포트 번호를 기반으로 패킷을 전달.
- L7 스위치
  - L4 스위치와 기능과 역할은 동일하나 페이로드를 분석하여 패킷을 처리함.
  - 페이로드를 분석함으로써 공격에 대한 방어, 감염 패킷 필터링 등 보안이 깅화됨.
  - 애플리케이션 계층에서 동작, URL을 기반으로 패킷을 전달.

### 브리지와 스위치의 차이는 무엇인가요?
- 처리 방식
  - 브릿지 : 소프트웨어 방식
  - 스위치 : 하드웨어 방식. 속도가 훨씬 빠름.
- 포트 수
  - 브릿지 < 스위치 : 스위치가 더 많은 포트를 제공하며. 포트 별로 속도를 지정 가능 

### L7 스위치와 L4 스위치의 차이는 무엇인가요?
- 기능과 역할은 동일하지만 L7 스위치는 페이로드를 분석하여 패킷을 처리함.
- L4 스위치는 IP 주소와 포트 번호를 기반으로 패킷을 전달하지만, L7 스위치는 URL을 기반으로 패킷을 전달함.

### L7 스위치가 로드 밸런싱에서 유리한 이유는 무엇인가요?
- L7 스위치는 페이로드를 분석하여 패킷을 처리하기 때문에, HTTP의 URL, 바이러스 패턴 등을 분석하여 로드 밸런싱을 수행할 수 있음.
- L4 스위치는 IP 주소와 포트 번호를 기반으로 패킷을 전달하기 때문에, URL을 기반하는 로드 밸런싱을 수행할 수 없음.(TCP/UDP만 가능)

# 3계층(네트워크 계층)
## 라우터
- 네트워크간 데이터 전송을 위한 최적 경로를 설정하고 데이터를 전송하는 장비
- L3 스위칭과 기능이 비슷하지만 가격이 비싸고 초기 설정이 복잡함.

### 종류
- static 라우터 : 수동으로 경로를 설정하는 라우터
- dynamic 라우터 : 라우팅 프로토콜을 사용하여 자동으로 경로를 설정하는 라우터
- IGP(Interior Gateway Protocol) : 동일 네트워크 내에서 라우팅 정보를 교환하는 프로토콜
- EGP(Exterior Gateway Protocol) : 다른 네트워크 간 라우팅 정보를 교환하는 프로토콜
- 거리 벡터 라우팅 : 홉 수를 기반으로 경로를 설정하는 라우팅 방식(거리와 방향을 기반으로 경로를 설정)
- 링크 상태 라우팅 : 링크 상태 정보를 기반으로 경로를 설정하는 라우팅 방식(네트워크의 상태 정보를 기반으로 경로를 설정)
