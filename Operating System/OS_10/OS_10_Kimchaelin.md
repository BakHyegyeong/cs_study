# 세마포어와 뮤텍스의 개념과 차이점
- 공유메모리를 통해 공유된 자원에 여러 개의 프로세스가 동시에 접근시 문제 발생하기에 접근을 동기화하는 방법

### 뮤텍스(Mutex)
<img src="https://hudi.blog/static/508725dc0fa8dc5e3d85493263e37a05/b331b/mutex.png" width="70%" height="70%">

- Key에 해당하는 오브젝트를 소유한 (쓰레드,프로세스) 만이 공유자원에 접근 가능
- 소유자가 뮤텍스를 해제해야만 다른 쓰레드가 뮤텍스를 획득할 수 있다.

### 세마포어(Semaphore)
<img src="https://hudi.blog/static/a0285ee59a5d4c7b8cc72d5ae3d6d3e2/034aa/semaphore.png" width="70%" height="70%">

- 공통으로 관리하는 하나의 값을 이용해 접근 관리하며, wait를 호출하면 세마포어의 카운트를 1 감소시키고, signal을 호출하면 세마포어의 카운트를 1 증가시킨다.
- 세마포어의 카운트가 0보다 작거나 같아질 경우에 락이 실행
- 소유자가 세마포어를 해제하지 않아도 다른 쓰레드가 Signal을 호출하여 세마포어를 해제할 수 있다.

#### 차이점
- 대상
    - Mutex는 대상이 오직 1개일 때 사용
    - Semaphore는 동기화 대상이 1개 이상일 때 사용
* 해제
    * Mutex는 소유하고 있는 스레드만이 뮤텍스를 해제할 수 있다.
    * Semaphore는 세마포어를 소유하지 않는 스레드가 세마포어를 해제할 수 있음.
* 자원 소유
    * Mutex: 한 스레드만 자원을 소유할 수 있고, 그 스레드가 자원을 사용 후 해제할 책임을 가짐.
    * Semaphore: 자원을 소유하는 스레드가 없고, 단순히 자원에 접근 가능한 스레드의 수만 관리. 자원에 대한 소유권이나 책임 개념이 없음.


# 프로세스 간 통신(IPC) 방식
- 프로세스끼리 데이터를 주고받으며 상호작용할 수 있도록 해주는 방법
- 각각의 프로세스는 독립된 메모리 공간을 사용하기 때문에 데이터를 교환할 때 IPC 기법이 필요하다.

### 주요 IPC 방식
#### 1. 파이프(Pipes)
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FddPAsa%2FbtsshqEIfhG%2FUzUkXBEdmZUqlmdDmQdkZK%2Fimg.png" width="70%" height="70%">

- 한 프로세스가 데이터를 쓰고 다른 프로세스가 읽는 방식
- 부모-자식 프로세스 간 통신에 사용
- 부모 프로세스는 쓰기 전용, 자식 프로세스는 읽기 전용
- 단반향 통신 방식

#### 2. 메시지 큐(Message Queue)
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbs3nRQ%2FbtssvvY5DJq%2F8G6TkOj6UKjHeBWSN7VxR0%2Fimg.png" width="70%" height="70%">

- 프로세스가 데이터를 큐에 넣고 다른 프로세스가 읽어가는 방식
- 단반향 비동기 통신 방식
- 내부적으로 버퍼를 사용하여 데이터를 저장하고 전달하므로, 데이터 손실이나 데이터 전달 지연이 발생 방지

#### 3. 공유 메모리(Shared Memory)
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbnOLMP%2FbtssfUfwnQX%2FEKTj1L9HOWkkfM1Tyi86PK%2Fimg.png" width="70%" height="70%">

- 여러 프로세스가 같은 메모리 영역을 공유하여 데이터를 빠르게 주고받을 수 있는 방식
- 속도는 빠르지만 여러 프로세스가 동시에 접근할 때 동기화 도구(세마포어, 뮤텍스)가 필요
- ex) 복사하고 붙여넣기

#### 4. 소켓(Sockets)
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdXgwCa%2Fbtssv0dzeYH%2FXvVSMcMs6sHiiQeVUGG8E0%2Fimg.png" width="70%" height="70%">

- 네트워크를 통해 다른 컴퓨터의 프로세스와 데이터를 주고받는 방식
- 클라이언트-서버 간 통신에 사용
- 프로토콜을 사용하여 데이터를 주고받음(예: TCP, UDP)
- 동기/비동기 통신 방식
