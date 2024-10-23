# OS_03
# 부팅이 되는 과정을 설명하시오.
#### 부팅이란?
컴퓨터에 전원을 공급하고 컴퓨터를 사용하기 위한 준비 과정

<img src="https://www.fun-coding.org/00_Images/boot.png">

#### 과정
- CPU는 ROM(Read Only Memory)의 BIOS(Basic Input Output System)를 실행
- BIOS는 RAM, 하드웨어 등 필수적인 주변 장치들이 잘 동작하는지 확인을 위한 테스트인 POST (Power On Self Test)를 동작시킴.
- 부트 로더가 있는 드라이브를 찾아서, BIOS는 Disk의 첫번째 섹터에 있는 MBR에 접근하여 부트 로더를 메모리에서 실행
- 커널 이미지 주소를 알아내어 메모리에 로드하고 최종적으로 OS를 실행하고 통제권을 줌
###### BIOS : 컴퓨터의 입출력을 처리하는 소프트웨어
###### 부트 로더 (Boot Loader) : 운영체제가 작동되기 전에 미리 실행되면서 커널이 올바르게 작동되기 위해 필요한 작업을 함.
###### MBR (Master Boot Record) : Disk의 첫 섹터이며, 해당 섹터안에 저장된 부트 로더를 실행 시킴.

# PCB란?
<img src="https://velog.velcdn.com/images/jjungyu12/post/dda4f271-740c-424f-8d16-b2f87a80f11b/image.png">

- 운영체제가 프로세스를 제어하고 관리하기 위해 프로세스의 모든 정보를 갖고 있는 블록
- 프로세스가 생성될 때마다 새로운 PCB가 생기고, 프로세스가 완료되면 PCB는 제거.

#### 목적
- 프로세스 상태 관리
- context switching시, 실행중이던 이전 프로세스를 PCB에 저장하고 실행할 프로세스 메타데이터를 PCB에서 읽어옴

##### 관리 방식
<img src="https://velog.velcdn.com/images/nnnyeong/post/55aa277d-f6b4-49af-acc2-3cc8494fd8ae/image.png">

- 운영체제는 빠르게 PCB 에 접근하기 위해, 프로세스 테이블을 사용함
- 프로세스 테이블은 PCB 실제 정보가 아닌 주소값을 저장

###### PCB 는 프로세스의 중요한 정보들을 담고 있으므로 아무나 접근할 수 없도록 보호된 메모리 영역에 존재
