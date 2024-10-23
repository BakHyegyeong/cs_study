## 부팅 과정

1. CPU는 전원이 켜지면 메모리의 0번지 주소에 있는 ROM에 있는 **BIOS를 실행**시키고 메모리(RAM)에 BIOS 프로그램이 올려진다.
    
    
    ![image.png](https://velog.velcdn.com/images%2Fhyun0310woo%2Fpost%2F06c430ee-f41e-4835-9184-05b8a4750a01%2FUntitled%20(5).png)
    
    - **ROM (Read-Only-Memory)** : 컴퓨터를 구동하기 위한 기본 정보가 담긴 메모리
        
        → 컴퓨터의 전원을 꺼도 메모리가 지워지지 않아 컴퓨터가 켜지면 ROM안의 정보를 읽어올 수 있다. 
        
    - **BIOS (Basic Input Output System)** : 하드웨어를 초기화시키고 저장매체의 MBR을 읽는다.
    - **MBR (Master Boot Record)** : 저장매체(디스크)의 첫 섹터에 저장되어 있으며 해당 섹터안에 부트 로더 프로그램이 저장되어 있다.
        
        → 운영체제가 어디에, 어떻게 위치해 있는지를 식별하여 컴퓨터의 주기억 장치에 적재될 수 있도록 하기 위한 정보이다.
        

1. 실행된 BIOS는 **POST(Power On Self Test)** 를 실행해 하드웨어에 이상이 없는지 확인한다.

1. BIOS는 저장매체의 첫번째 섹터에 있는 MBR에 접근하여 부트 로더를 메모리에서 실행시킨다.
    
    ![image.png](https://velog.velcdn.com/images%2Fhyun0310woo%2Fpost%2F28783e7e-abb5-4237-b4e6-6003ab17afb8%2FUntitled%20(6).png)
    
    → 실행된 부트 로더 프로그램은 파티션 테이블을 메모리에 올리며 이 파티션 테이블 안에서 **메인 파티션(운영체제가 들어있는 파티션)** 을 찾는다.
    
    - 부트 로더 : 파티션들의 정보가 들어있는 파티션 테이블이 존재한다.
    
    > 💡  **파티션** : 물리적인 디스크를 여러 개의 논리적인 부분으로 나누어 사용하기 위한 단위 <br>
        → 물리적인 디스크를 나누는 단위
    
   
    
    
2. 메모리는 부트 섹터에 접근해 부트 코드를 실행시켜 운영체제 이미지를 메모리에 올려 운영체제를 실행시킨다.
    
    ![image.png](https://velog.velcdn.com/images%2Fhyun0310woo%2Fpost%2F08b00ad8-d119-4dc8-a57d-771b38ecdf45%2FUntitled%20(7).png)
    
    → 운영체제가 들어있는 메인 파티션 안의 부트 섹터에 있는 부트 코드를 실행시킨다.
    
    ![image.png](https://velog.velcdn.com/images%2Fhyun0310woo%2Fpost%2Fcd379ab8-d316-4958-a049-44713d0b18f5%2FUntitled%20(8).png)
    
    → 실행된 부트 코드는 운영체제의 이미지를 메모리에 올려 운영체제가 실행된다.
    
<br>

## PCB란?

: 프로세스에 대한 메타데이터를 저장한 데이터

→ 프로세스가 생성되면 운영체제가 해당 프로세스의 PCB를 생성한다.

- 프로그램 실행
- 프로세스 생성
- 프로세스 주소 공간(Code Segment, Data Segment, Stack) 생성
- 프로세스의 메타데이터들이 PCB에 저장

### 필요한 이유

: CPU는 프로세스의 상태에 따라서 **교체 작업(Context Switching)**이 이루어지는데 이때 **교체되는 프로세스의 상태 값을 PCB에 저장**해두었다가 나중에 다시 프로세스를 실행할 때 사용한다.

### 저장되는 정보

![image.png](https://images.velog.io/images/klloo/post/5562a831-dc1e-4e95-b590-25cef88bd43e/image.png)

- Process state
- CPU scheduling information : 프로세스의 우선 순위, 최종 실행 시간, 스케쥴링 큐를 가리키는 포인터 등
- Memory management information : 레지스터, 페이지 테이블, 세그먼트 테이블의 base, limit값에 대한 정보
- Accounting information : CPU 사용 시간, 실제 사용된 시간, 시간 제한 등