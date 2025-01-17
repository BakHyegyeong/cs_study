# 프로세스 스케줄링이 필요한 이유

프로세스 스케줄링은 **CPU와 같은 자원을 효율적으로 사용**하고, **프로세스의 공정한 실행**을 보장하기 위해 필요하다

1. **CPU 자원의 효율적 사용** : 스케줄링을 통해 CPU 유휴 시간을 최소화하고, 각 프로세스가 CPU 시간을 균등하게 사용할 수 있게 한다.
2. **응답 시간 개선** : 사용자 요청에 빠르게 응답하기 위해 긴급한 프로세스를 우선적으로 처리하여 시스템 응답성을 높일 수 있다.
3. **공정성 보장** : 다중 프로세스 환경에서 특정 프로세스가 자원을 독점하지 않도록 하여 모든 프로세스가 공평하게 CPU를 사용할 수 있도록 한다.
4. **다중 사용자 환경 지원** : 여러 사용자의 요청을 동시에 처리하여 사용자 경험을 개선하고 시스템의 성능을 최적화할 수 있다.

--- 
# 선점형 스케줄링과 비선점형 스케줄링

## 선점 스케줄링 (Preemptive Scheduling)

선점 스케줄링은 현재 실행 중인 프로세스를 **중단하고, 더 높은 우선순위의 프로세스에 CPU를 할당**하는 방식이다. 실시간 시스템과 같이 응답 속도가 중요한 환경에서 주로 사용된다.

- 빠른 응답 시간, 문맥 전환 오버헤드 존재
- **대표 알고리즘**
    - **Round Robin (RR)** : 각 프로세스가 정해진 시간 동안 CPU를 사용하고, 시간이 다 되면 다음 프로세스로 전환
    - **Shortest Remaining Time First (SRTF)** : 남은 실행 시간이 가장 짧은 프로세스에 우선적으로 CPU를 할당
    - **Priority Scheduling** : 우선순위가 높은 프로세스가 먼저 실행

## 비선점 스케줄링 (Non-Preemptive Scheduling)

비선점 스케줄링은 **프로세스가 자발적으로 종료하거나 입출력 요청이 있을 때만 CPU를 다른 프로세스에 할당**하는 방식이다. 배치 작업 등 긴급성이 낮은 시스템에 적합하다.

- 문맥 전환 오버헤드가 적고, 응답 시간은 느림
- **대표 알고리즘**
    - **First-Come, First-Served (FCFS)** : 도착 순서대로 프로세스를 실행
    - **Shortest Job First (SJF) :** 실행 시간이 가장 짧은 프로세스를 먼저 실행
    - **Priority Scheduling** : 우선순위에 따라 순서대로 프로세스를 실행

👇🏻 위의 CPU 스케줄링 알고리즘을 시각화해서 보고 싶다면 아래 링크에서 확인해보자
https://cpu-scheduling-algorithm-visualiser.netlify.app
