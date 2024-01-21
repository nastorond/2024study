# Chapter 3. Processes 1.

- A process is a program in execution  
: 실행중인 프로그램

- A process is the unit of work in an operating system.  
OS의 역할 : 프로그램을 운영시키는 것, 작업의 단위:프로세스

- A process will need certain resources to accomplish its task
  - CPU time
  - memory
  - files, I/O devices (resources)

- Computer 구조 : CPU와 memory가 연결되어있고, memory에서 instruction들을 하나씩 fetch해와서 CPU에서 execute 하는 것

  - 실행파일(a.out ; a set of instruction)이 HD, SSD (storage)에 저장되어 있는데, 이것을 memory에 로드하면(memory상태에 있는 program을 process:program in execution이라함) CPU에서 fetch해서 실행.  
  - CPU를 점유 : timesharing을 해서 여러 process가 동시에 CPU를 공유하고 있기 때문에 동시적으로 실행하려면 CPU를 점유해야 한다. 
  - OS가 하는 일 : process를 관리

### Process의 구조
- THe memory layout of a process is divided into multiple sections:
  - Text section : the executable code(명령어 나열)
  - Data section : global variables(전역변수)
  - Heap section : memory that is dynamically allocated during program run time(메모리 할당)
  - Stack section : 
    - temporary data storage when invoking functions such as function parameters, return addresses, and local variables(함수 호출 시 저장)

- gcc main.c 파일 -> a.out (program)
- gcc main.c
- size ./a.out

### Process life cycle
- OS 가 process를 관리한다 - 어떻게 관리하냐 
- As a process executes, it changes its state
  - New : 프로세스 생성
  - Running : CPU를 점유해서 프로세스의 명령어를 CPU에 로드해서 실행
  - Waiting : CPU 입장에서 다른 프로세스(I/O나 이벤트 wait가 있을 때)
  - Ready : CPU 점유 대기상태
  - Terminated : 프로세스 종료(메모리, 자원 회수)
    - ready&rarr;running : scheduler dispatch

### PCB(Process Control Block) 
  - PCB 구조체에 특정 process가 가져야 되는 정보를 모두 저장하고 process를 핸들링
  - process state: new,running,wait, ready, terminated
  - context
    - Program counter(PC) : Instruction Register(IR) (PC의 번지 정보에 해당하는 메모리에 있는 명령어를 fetch)
    - CPU registers : IR(Instruction Register), DR(Data Register)
  - CPU-scheduling information : CPU를 주고받기 위한 스케쥴링 정보들
  - Memory-management information(mlock)
  - Accounting information (유저 계정)
  - I/O status (파일, 자원)
&rarr; typedef struct ; 이 PCB를 OS가 관리

### Process Concept

Process는 "single thread of execution"을 수행하는 프로램이다.
- 하나의 실타래
- OS의 입장에서는 CPU 1개, CPU에는 Program Counter, Instruction Register, Data Register 있다. PC에 따라 메모리에있는 여러개의 process(single thread of execution)을 순차적으로 실행한다. PCB를 전환하게 되면 process를 전환하는 게 된다. 
- P0를 실행하다가 PCB 블록을 전환하고 P1을 실행하고, 또 전환하면 P0를 이어서 실행한다. 
- 1 task at a time 

### Multi processing
- Modern operating systems allow process to have multiple threads of execution(concept of process has extended)

- **멀티태스킹, 멀티프로세싱이 현대 운영체제의 주요 기능이다.**

### Thread
: Lightweight process
- OS가 PCB를 이용해서 여러 process를 timesharing 하듯, Process 안에서도 여러개로 나눠서 하는 개념
- **Multi Threading**이 대부분 사용된다

### Process Scheduling

- 멀티프로그래밍(멀티 프로세싱)의 목적 : run programs simultaneuously/concurrently.(not parallel)
  - have some process run at all times
  - maximize CPU utilization
- Time sharing의 목적
  - CPU core를 프로세스간에 자주 스위치해서 사용자 입장에서 각 프로그래밍이 동시에 run하는 것처럼 보이도록 한다.
&rarr;이를 실행하기 위해서는 CPU를 scheduling 해야됨
- Scheduling Queues:
  - process가 enter하면 ready queue에 put. 여기서 FIFO로 CPU core에서 execute 되기를 ready and waiting 한다. 
  - as processes enter the system, they are put into a ready queue, where they are ready adn waiting to execute on a CPU's core
  - ready queue에서 CPU로 가서 running 되고 다시 ready queue로 간다.
  - i/o가 있다면 wait queue로 가고 io completion이 끝나면 ready queue로 다시 간다.
  - linked List로 구현 


### Context Switch(문맥 교환)
- Context : PCB에 저장되어 있다. PCB 정보. 
- Interrupt가 일어나면, running process의 current context를 저장해두고(PC), 
- 다시 resume될 때(ready queue &rarr; running) restore the context 를 restore한다.
- Context switch : CPU core에 다른 process로 switch 할 때 현재 process의 state를 save + 다른 process 의 pcb 복원하는 task

### Operations on Processes
- parent process, child process
- fork()라는 system core를 활용해서 O/S가 실행되면 init process를 생성
- Process Tree 생성
- Terminate: final statement / exit()
  - 메모리 , file, i/o 회수
- Zombie and Orphan process

### Process 의 생성

#### UNIX-like O/S에서는:

- fork()로 프로세스 생성
- child process는 parent process의 "copy of the address space"로 이루어짐
- child process : fork()의 return 값이 0
- parent process : nonzero pid of child is returned to parent process

#### fork() system call 이후에,

- parent 는 continue execution 하거나
- child가 run하는 동안 wait() system call을 해서 ready queue에서 벗어나 wait queue에서 child process의 termination을 기다림
