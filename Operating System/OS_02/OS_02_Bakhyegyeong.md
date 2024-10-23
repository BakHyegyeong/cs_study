## 프로세스의 5가지 상태

![image.png](https://yanghs6.github.io/assets/img/computer_science/1003/1003_01_process_state.png)

1. 생성 ( Create ) : 프로세스 생성 
    
    → 이때 PCB를 할당받는다.
    
    > 💡 **PCB (Process Control Block) :** 프로세스에 대한 메타데이터를 저장한 데이터 <br>
     → 프로세스가 생성되면 운영체제가 해당 프로세스의 PCB를 생성한다.
    
    
2. 준비 ( Ready ) : 메모리를 할당받고 프로세스가 CPU를 할당받기를 기다리는 상태
3. 실행 ( Running ) : 프로세스가 CPU를 할당받아 명령어들을 실행
4. 대기 ( Waiting 또는 Blocked ) : 프로세스가  이벤트나 입출력 신호가 완료될 때까지 기다리는 상태
5. 종료 ( Terminated ) : 프로세스 실행 종료

### 프로세스의 상태 심화

![image.png](https://velog.velcdn.com/images/mingadinga_1234/post/ba910ce9-889b-4671-9a33-995f36733124/image.png)

**생성 - 메모리를 할당받았는지 여부**

→ 준비 ( Ready ) : 메모리를 할당받은 후 CPU를 할당받기를 기다리는 상태

→ 준비 보류 ( Ready Suspended ) : 메모리 부족으로 일시 중단 <br>
→ 메모리를 받을 경우 다시 준비 상태가 된다.

**실행**

→ 준비 (Timeout으로 인한 Ready) : 할당된 시간을 전부 소진하여 다시 Ready 상태가 된다.

→ 대기 (Blocked) : 이벤트 또는 입출력이 완료될 때까지 대기

**대기**

→ 준비 : 입출력이 완료되어 다시 CPU를 할당받기를 기다리는 상태

→ 보류 대기 ( Blocked Suspended ) : 대기 상태에서 메모리 부족으로 일시 중단
→ 메모리를 받을 경우 다시 대기 상태가 된다. <br>
→ 메모리를 받지 못하고 이벤트나 입출력 상태가 완료된다면 준비 보류 상태가 된다.


---

## 프로세스의 메모리 구조

: 프로세스의 메모리는 **스택, 힙, 데이터 영역(BSS segment, Data segment), 코드 영역(Code segment)** 로 이루어져 있다.


![image.png](https://i.imgur.com/21kg8t5.png)

### 메모리 내 영역

- 정적 영역 : 컴파일 단계에서 메모리를 할당받는 것
    - 스택, 데이터 영역, 코드 영역
- 동적 영역 : 런타임 단계에서 메모리를 할당받는 것
  - 힙

### 동적 영역
    

1. Heap : **동적으로 할당되는 변수들**이 저장되는 메모리 영역이다. 선입선출 방식이다.
    
    → 메모리의 낮은 주소에서 높은 주소의 방향으로 할당된다.
    

> 💡 **stack overflow, heap overflow** : stack과 heap이 **같은 영역을 공유**하기 때문에 서로의 영역을 침범하는 것


### 정적 영역

1. Stack : **지역 변수, 매개변수**가 저장되며 실행되는 함수에 의해 크기가 달라지는 메모리 영역이다. 후입선출 방식이다.
    
    → 이 때문에 메모리의 마지막 번지, 높은 주소에 저장되어 낮은 주소의 방향으로 할당된다.
    
    ![image.png](https://i.imgur.com/njBJcpH.png)

    [stack은 정적 영역이지만 런타임 단계에서 메모리 크기가 달라진다.](https://www.inflearn.com/community/questions/981575/%EC%8A%A4%ED%83%9D-%EC%98%81%EC%97%AD%EC%9D%98-%ED%81%AC%EA%B8%B0%EB%8A%94-%EC%A0%95%EC%A0%81%EC%9D%B8%EA%B0%80%EC%9A%94-%EB%8F%99%EC%A0%81%EC%9D%B8%EA%B0%80%EC%9A%94)
2. Bss segment : **초기화되지 않은** 전역 변수, 정적 변수
3. Data segment : **초기화된** 전역 변수, 정적 변수
4. Code segment : 프로그램의 코드

> 💡 **BSS영역, Data영역을 구분하는 이유?** <br>
  : 초기화 된 데이터는 초기 값을 저장해야 하므로 Data 영역에 저장되어 rom에 저장된다. 하지만 초기화 되지 않은 데이터 까지 rom에 저장되면 큰 size의 rom이 필요하므로 이를 분리한다. ( 초기화 되지 않은 데이터는 ram에 저장)

❗ **static으로 선언된 arraylist는 어디에 저장될까** <br>
: arraylist 자체는 초기화되었으므로 data segment에, add() 메서드 등을 통해 동적으로 할당되는 객체들은 heap 영역에 저장된다.