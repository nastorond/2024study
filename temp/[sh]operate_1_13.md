# 운영체제
- a software that operates a computer system
## computer
### computer is 
- a machine that processes the *information*
### information
- I(x) = -log_2P(x)
- 불확실성을 측정헤서 수치적으로 표현한 것
### 컴퓨터는 정보를 어떻게 처리하는 가
- 최소 단위 bit(**b**inary dig**it**)
- 8 bit = 2^8
- 정보의 처리 : 정보의 상태 변환 (0에서 1로, 1에서 0으로)
- Boolean Algebra(대수) : NOT, AND, OR
- 논리 게이트 : NOT, AND, AND, OR, XOR, NAND, NOR
- 논리 회로 : IC, LSI, VSI, ULSI, SoC, ...
    - 무어의 법칙, 황의 법칙
- 정보의 저장과 전송 : 플립 - 플롭, 데이터 버스, Radio Frequency
    - 덧셈 : 반가산기, 전가산기
    - 뺄셈 : 2의 보수 표현법
    - 곱셈과 나눗셈 : 덧셈과 뺄셈의 반복
    - 실수 연산 : 부동 소수점 표현
    - 함수 : GOTO
    - 삼각함수, 미분, 적분, 사진촬영 등..
- 범용성 universality
    - NOT, AND, OR게이트 만으로 모든 계산을 할 수 있다.
    - NAND 게이트 만으로 모든 계산 가능
    - general-purpose computer &rarr; computer
- 계산 가능성 computability
    - Turing-computable 튜링 머신으로 계산가능한 것
    - 정지 문제 Halting Problem : 튜링 머신으로 풀 수 없는 문제
### 앨런 튜링
- Head, Tape,   Turing Machines, Universal Turing Machine
- CPU,  Memory, aplication,      Operating System
### 폰 노이만
- cpu -bus&rarr; RAM
- a stored-program computer
    - 메모리에 프로그램을 저장하는 컴퓨터
    - 프로그램은 명령어 들을 가지고 있고 명령어 들을 하나씩 cpu가 fetch를 하고 execute 함
    - cpu가 가지고 와서 실행
    - ISA
### 프로그램
- a set of instructions that tells a computer's hardware to perform a task
- 명령어 들의 집합
## 운영체제도 프로그램인가
- 운영체제 역시 프로그램
- 항상 실행 되는 프로그램
- is a program running at all times on the computer
- to provide system services to application programs
- to manage ***processes***, resources, user interfaces, and so on.
# 운영체제 개념 구조
- classical computer system
    - 한개 이상의 cpu, 여러개의 컨트롤러디바이스 through a common bus
    - RF, bluetooth
### Bootstrap 프로그램
- a first program to run on computer power-on 
- 운영체제 로딩
### Interrupts
- I/O 디바이스에서 입력이 되면 cpu 에서 받아서 처리할 때 사용하는 통신장치
- Hardware may trigger an interrupt at any time by sending a signal to the CPU, usually by way of the system bus
### 폰노이만 아키텍쳐
- instruction-execution cycle
    - instructer register
    - 명령 레지스터
    - 데이터 레지스터
    - alu
    - fetch - execute cycle
- How a morden computer system works
    - CPU가 Thread of Execution을 가지고서 cache를 통해 RAM에 access
    - IO 디바이스가 Inturrpt를 걸고 데이터를 주고 받음
## computer system arcitecture
### computer system component
    - cpu, processor, core, multicore, multiprocessor
### Symmetric multiprocessing SMP
- 메모리가 한갠데 CPU가 여러개가 연결되어 있음
### Multi-core design
- with several cores on the same processor chip
### Multiprogramming
- 프로그램 : a set of instruction
- 프로그램 여러개를 동시에 올리는 것 : runs more than one program at a time.
- cpu 사용 효율이 오름
### Multitasking (Multiprocessing) 
- 하나의 cpu를 가지고 여러개의 작업이 가능
- a logical extension of multiprogramming
    - in which CPU switches jobs so frequently that
    - users can interact with each job while it is running
- CPU scheduling 
    - 여러개의 프로세스가 램에 존재할 때 cpu가 실행 시켜야 할 때 어떤 프로그램을 선택할 지 고르는 것
### Two separate mode of operations
- user mode and kernel mode
- 바이러스, 엉뚱한 작업을 실행했을 때 막아주는 역할도 운영체제가 해야할 역할
- to ensure that an incorrect program cannot cause other programs to execute incorrectly
- 커널 모드가 아니면 직접적으로 하드웨어를 제어하지 못하게
## Virtualiztion
- 가상화 기술
- a technology that allow us
    - to abstract the hardware of single computer
    - into several different execution environment
- VMM : Virtual Machine Manager(Moniter)
    - 하드웨어가 있을때 이곳에 os를 바로 올리는 것이 아니라 VMM을 올리고 여러개의 os를 올리고 동시에 여러개의 os를 가동
    - 하드웨어에 VMM이 있고 각각의 VM이 커널을 이용해 프로그램 실행
## Computing Environment
- Traditional Computing
- Mobile Computing : Android, Ios
- Client-Server Computing
- Peer to Peer(p2p) Computing : 음악, 영화 공유
- Cloud Computing : Edge Computing
- Real-Time Embedded Systems
- Saas : Software as a Service
## User and Operating System Interface
- CLI : Commend Line Interface 
- GUI : Graphical User Interface
- Touch-Screen Interface
## System Calls
- provide an interface to the services made available by the OS.
- API : Application Programming Interface
- OS의 API가 System call 이라고 보면 댐
