## Process
### 실행중인 프로그램  
$\rarr$ 프로세스는 OS에서 일의 단위  
$\rarr$ 즉, 스토리지에 있는 프로그램을 메모리로 로드한 상태  
$\rarr$ 일을 성취하기 위해 자원들(CPU time, memory, files, I/O devices)이 필요함   

### 프로세스의 메모리 layout  
<img src="./img/memoryLayout.PNG" height = 300px>  

$\rarr$ Text section : executable code(명령어)  
$\rarr$ Data section : global variables(전역변수)  
$\rarr$ Heap section : memory that is dynamically allocated during program run time(ex. Java의 new)  
$\rarr$ Stack section : temporary data storage when invoking functions(함수 실행 시 : function parameters, return addresses, local variables)  

$\rarr$ source code를 컴파일 : 프로그램  
$\rarr$ 프로그램 로드 : 프로세스  

## Process Concept 
### 5개의 state 존재
<img src="./img/processState.PNG" height = 200px>

$\rarr$ New : the process is being created(탄생 상태)  
$\rarr$ Running : Instructions are being executed(CPU 점유하여 로드 상태)  
$\rarr$ Waiting : the process is waiting for some event to occur  
(일의 종료 전 Time sharing으로 인해 대기 such as an I/O completion or receptin of a signal)  
$\rarr$ Ready : the process is waiting to be assigned to a processor(할당 대기 상태)  
$\rarr$ Terminated : the process has finished execution(종료 상태)  

### PCB(Process Control Block) or TCB(Task Control Block)
$\rarr$ 구조체 하나에 프로세스가 가져야 하는 모든 정보 저장  
#### PCB가 가져야하는 정보
$\rarr$ Process state  
$\rarr$ Program counter(다음에 실행될 명령어의 주소를 가지고 있어 실행할 기계어 코드의 위치를 지정)   
$\rarr$ CPU registers  
$\rarr$ CPU-scheduling information  
$\rarr$ Memory-management information  
$\rarr$ Accounting information  
$\rarr$ I/O status information  

### Process
$\rarr$ a program that performs <b>a single thread of execution</b>  
$\rarr$ only one task at a time  
+) multitasking, multiprocessing 차이  
$\rarr$ Mordern operating systems have extended the process concept  
to allow a process to have multiple threads of execution  
and thus to <b>perform more than one task at a time</b>   

### Thread
lightweight process  
$\rarr$ 프로세스를 여러개 하는 것보다 tread를 여러개로 작업하는 것이 장점이 훨씬 많음  
+) (어떤거지?)  

## Process Scheduling
#### multiprogramming의 목적
$\rarr$ to have some process running at all times(is not parallel)  
$\rarr$ so as to maximize CPU utilization
#### Time sharing 목적
$\rarr$ to switch a CPU core among processes so frequently  
$\rarr$ that users can interact with each program while it is running  

### Scheduling Queues
<img src="./img/schedulingQueues.PNG" height = 350px>

linked lists of PCBs로 구현  
#### ready queue  
As processes enter the system, they are put into a ready queue,  
where they are ready and waiting to execute on a CPU's core
#### wait queue
Processes that are waiting for a certain event to occur  
are placed in a wait queue  
(wait queue가 끝나면 running으로 가는 것이 아닌 ready로 가서 기다린다.)  

### Queueing Diagram
<img src="./img/queueingDiagram.PNG">

### Context Switch(문맥 교환)
$\rarr$ The context of a process is represented in the PCB  
$\rarr$ Interrupt 발생 시  
실행중인 프로세스의 현재 context 저장(이때, 어디까지 실행되었는지 PC(Program counter) 저장 중요)  
이후, 재할당 되면 저장된 context를 restore  
#### context switch
<img src="./img/contextSwitch.PNG" height = 350px>

$\rarr$ switches the CPU core to another process  
$\rarr$ performs a state <b>save</b> of the current process  
$\rarr$ and a state <b>restore</b> of a different process  

## Operations on Processes
운영체제는 process creation, and process termination에 대한 메커니즘 제공  
### Process Create
#### 프로세스는 새로운 프로세스 create 가능  
the creating process: a parent process  
a newly created process: a child process  
+++++++++++++이 밑에 사진 이해 잘 안됨 중요하다 했는데...  
<img src="./img/processCreation-fork().PNG">

#### Two possibilities for execution
$\rarr$ The parent continues to <b>execute concurrently </b>with its children(부모도 동시에 계속 실행)  
$\rarr$ The parent <b>waits</b> until some or all of its children have terminated(부모는 잠시 wait)

#### Two possibilities of address-space
$\rarr$ The child process is a <b>duplicate</b> of the parent process  
(똑같은 일을 하는 process인 경우 굳이 별도의 영역을 잡지 않고 PCB(PC)만 따로 존재하여 실행 위치만 다르면 됨)  
$\rarr$ The child process has a <b>new program</b> loaded into it

### Process Terminate
$\rarr$ final statement 실행  
$\rarr$ exit() system call: asks OS to delete it(중간 강제 종료)  
$\rarr$ OS deallocates(할당 해제) and reclaims(회수) all the resources(allocated memories, open files, and I/O buffers, etc)  

### Zombie and Orphan
#### zombie process
$\rarr$ a process that has terminated, but whose parent has not yet called wait()
(부모가 죽진 않았지만, wait하지 않음)
#### orphan process
$\rarr$ a process that has a parent process who did not invoke wait() and instead terminated
(wait없이 return으로 종료 시 부모없는 자식 생김)

