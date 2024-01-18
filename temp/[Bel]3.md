# Chapter 3
## Process 1
### Process Concept
- 실행 중인 프로그램을 프로세스라고 한다.
- single thread 의 실행을 하는 프로그램이 프로세스다.
- 프로그램들을 실행시키는 운영체제 입장에서 프로세스를 실행시키기 위해서는
    - CPU
    - memory
    - files
    - I/O device 
가 필요하다.

- 여러 개의 섹션으로 나눠져 있는 프로세스의 메모리 layout
    - Text section
        - 실행시키기 위한 명령어들
    - Data section
        - 전역 변수들
    - Heap section
        - memory가 location 했을 때
    - Stack section
        - 함수 호출하면 함수 stack이 쌓임

- logical하게 어떤 프로그램이 시작하는 부분을 0, 최대 메모리 영역이 max라고 했을 때
    - stack은 max 부터 아래로 내려감
    - 아래에서부터 text, data, heap 순으로 올라감

    - malloc(sizeof(int) * 4) 
        &rarr; 동적할당 - 프로그램 실행 중에 동적으로 메모리를 할당 하는 것  
        &rarr; 동적 할당한 메모리는 꼭 마지막에 해제해 주어야 함  
        &rarr; 메모리 릭, 메모리 누수가 발생하기 때문에  

- 프로세스의 life cycle의 5개 state
    - New
        - 프로세스가 처음 생성된 상태
    - Running 
        - 프로세스의 명령어를 CPU가 실행한 상태
    - Waiting 
        - CPU의 Time Scheduling을 통해 2개의 프로세스가 실행될 때 실행되지 않고 기다리고 있는 process의 상태
        - I/O가 끝날 때 까지 대기하는 상태
    - Ready
        - I/O가 끝나고 나서 프로세스가 실행 되기 위해 준비하는 상태
    - Terminated
        - 다 끝난 상태

### PCB(Process Control Block) or TCB(Task Control Block)
- PCB : process에 모든 것을 다 저장하고 handling하자

- Process state 
- Program counter
- CPU registers
- CPU-scheduling information
- Memory-management information
- Accounting information
- I/O status information

### A process is
- 시 분할을 통해 실행되는 single thread of execution
- 여러 개의 process가 동시에 실행되는 것이 multiprocessing(multitasking)


- thread(쓰레드)
    - 위의 개념의 thread와 다른 것
    - 4장에서 다시 볼 것임

#### multiprogramming
- 여러 개의 process를 동시에 실행하는 것
- CPU utilization을 극대화 하기위해 사용

- time sharing 
    - CPU core를 바꿔가며 빈번하게 process를 실행함으로서 동시에 실행되는 것처럼 구동
- Scheduling Queues(FIFO)
    - ready queue : process가 CPU에서 실행되기까지 ready state 상태로 있다가 CPU를 얻는 것
    - wait queue : process가 I/O에 있으면 wait queue에 넣고 대기하다가 ready queue로 옮김
    - linked list로 구현하면 어렵지는 않음

### Queueing Diagram  
    - CPU가 queue를 획득하고 나서 
        1. 바로 ready queue로 가서 실행되는 경우
        2. I/O request가 왔을 때
            - I/O request &rarr; I/O queue &rarr; I/O &rarr; ready queue
        3. time slicer가 expired 되었을 때 기다릴 것이 없이 바로 ready queue로 감
            - time slice expired &rarr; ready queue
        4. CPU가 fork해서 새로운 상태가 되었을 때
            - fork a child &rarr; child executes &rarr; ready queue
        5. interrupt를 기다리고 있을 때
            - wait for an interrupt &rarr; interrupt occurs &rarr; ready queue

### Context Switch(문맥 교환)
- 어떤 interrupt가 일어났을 때 문맥을 교환하는 일을 O/S가 해줘야 함
- 어떤 task가 CPU core를 다른 process에게 넘겨주는 2가지 작업
    1. 현재 process의 state를 저장
    2. 다른 새로운 process의 state를 보관