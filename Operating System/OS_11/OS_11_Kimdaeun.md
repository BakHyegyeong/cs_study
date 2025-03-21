# 데드락이란(Deadlock)?
Deadlock(교착 상태)는 여러 프로세스가 서로의 자원을 기다리면서 무한정 멈춰  있는 상황을 뜻한다. 
![](https://velog.velcdn.com/images/abcdana/post/40846710-6025-467a-b6ea-c30ffec9996d/image.PNG)


위의 다이어그램을 보면 프로세스1이 자원1을 점유하고 있고 자원2를 필요로한다.

프로세스2는 자원2를 점유하고 있으며 자원1을 필요로 하고있다.

→ 두 프로세스가 서로의 자원을 기다리면서 교착상태에 빠지게 된다. 각 프로세스들은 실행을 완료하기 위해 상대방의 자원이 필요하지만 둘 다 현재 자원을 포기하지 않은 상태이기 때문이다. 

# 데드락이 발생하는 4가지 조건

1. **상호 배제 (Mutual Exclusion)** : 특정 자원은 한 번에 하나의 프로세스만 사용할 수 있어야 한다. 자원 1이 프로세스 1에 의해 점유되고 있으면 다른 프로세스는 사용할 수 없다.
2. **점유와 대기 (Hold and Wait)** : 프로세스가 자원을 점유한 상태에서 추가 자원을 요청할 수 있다. 프로세스 2가 자원 2와 자원 3을 점유하고, 자원 1을 요청할 수 있다.
3. **비선점 (No Preemption)** : 자원은 강제로 다른 프로세스에 의해 빼앗길 수 없다. 프로세스는 스스로 자원을 자발적으로 해제해야 한다.
4. **환형 대기 (Circular Wait)** : 프로세스들이 서로 순환 구조로 자원을 기다리는 상태다. 프로세스 1이 자원 2를 점유하고 자원 1을 기다리고, 프로세스 2는 자원 1을 점유하고 자원 2를 기다리는 경우에 환형 대기 상태가 된다.

# 데드락 예방 및 회피 방법

### 예방 방법

- **자원 점유 금지** : 프로세스가 자원을 점유한 상태에서는 다른 자원을 기다리지 않도록 한다.
- **선점 허용** : 자원이 점유된 상태에서 다른 프로세스가 자원을 요청할 경우 강제로 빼앗아 올 수 있도록 허용한다.
- **환형 대기 방지** : 자원을 일정한 순서로만 요청하도록 제한해 환형 대기를 방지한다.

### 회피 방법

1. **은행원 알고리즘 (Banker’s Algorithm)**
    
    프로세스가 자원을 요청할 때 시스템이 안전 상태인지 확인하여 교착 상태를 피할 수 있는지 평가한다. 시스템이 안전 상태일 때만 자원을 할당한다.
    
    - **안전 상태** : 모든 프로세스가 실행될 수 있도록 필요한 자원을 순서대로 제공할 수 있는 상태
2. **자원 할당 그래프 사용**
    
    프로세스와 자원 간의 관계를 그래프로 표현하여 교착 상태가 발생할 가능성을 감지하고, 문제가 될 상황이 오기 전에 자원을 할당하거나 대기를 조정한다.
