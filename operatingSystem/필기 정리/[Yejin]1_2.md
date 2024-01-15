### 운영체제의 개녕과 구조
Introduction & O/S structures  

운영체제 : 컴퓨터의 하드웨어를 관리하는 소프트웨어  
    -> 사용자와 하드웨어 사이의 중개 역할  
    -> 컴퓨터에서 항상 실행중인 프로그램  
    -> kernel  
        -> system programs  
        -> application programs  
    -> CPU, 장치 컨트롤러들과 bus를 통해 연결

컴퓨터의 4개 컴포넌트  
1 하드웨어   
2 O/S   
3 어플리케이션 프로그램  
4 사용자  

#### bootstrap program
부팅용 프로그램 : 컴퓨터가 power on 되자마자 처음으로 동작되는 프로그램  
    -> 하드디스크에 있는 O/S를 메모리로 로딩 시킴  
+) EEPROM (Electrically Erasable Programmable Read-Only Memory, E2PROM) : 비휘발성 메모리(NVM)의 하나 -> CPU가 가장 먼저 로딩해야 할 명령어를 저장해 놓은 곳

#### Interrupts

I/O device(키보드)에서 A가 눌렸음을 CPU에게 알려주는 방식
-> 하드웨어는 보통 시스템 버스를 통해 CPU로 신호를 전송함으로써 언제든지 인터럽트를 트리거할 수 있음

#### von Neumann architexture
: instruction-execution cycle  
: 명령어 집합(instruction set)에 구성된 명령어들로 구성된 프로그램(a set of instructions)을 메모리에 로딩 시 cpu가 메모리에 있는 명령어를 하나씩 fetch, execute 진행  
+) IR, ALU

: storage system은 여러 계층구조 존재  
-> 용량(storage capacity), 접근 속도(access time)에 따라  
registers -> cache -> main memory -> solid-state disk(SSD) -> hard disk(HDD) -> optical disk -> magnetic tapes 

#### I/O Structure
CPU가 실행과정(thread of execution)을 가지고 있을 때,  
cache 메모리를 통해 RAM에 접근하고  
I/O device가 interrupt 걸어 data를 주고 받음  
이때, device가 cpu를 거치지 않고 memory에 바로 접근하는 것은 DMA(Direct Memory Access) 구조  
현재 운영체제의 경우 kernel 개발은 거의 안정화된 상태라  
추가되는 device에 대한 controller 개발에 집중되고 있음  

#### computer system components
1 CPU : 명령을 처리
2 processor
3 core
4 muticore
5 multiprocessor

#### SMP(Symmetric multiprocessing)
CPU : memory = 1:1 구조를 초소형 임베디드를 제외하고 거의 사용하지 않음  
현재의 경우 SMP 탑재  
1개의 메모리에 여러개의 CPU(각각의 register, cache를 가지고 있음) 연결  

#### Multi-core design
같은 프로세서 칩 하나에 register와 cache를 가지고 있는 CPU core만 여러개 붙여 따로 회로로 구성

#### Multiprogramming
여러개의 프로그램을 동시에 메모리에 올려 실행  
-> 하나 이상의 프로그램이 동시에 작동하는 것  
-> 메모리에 여러개의 프로세서가 동시에 올라가 CPU 사용 효율을 높임  

##### Multitasking(=multiprocessing)  
-> 시분할(time-sharing)  
-> concurrency(!= parallelism)  
-> 하나의 CPU가 여러개의 일을 자주 바꿔주면 사용자의 입장에서는 여러개의 일을 동시에 할 수 있음  

-> CPU scheduling : 
    