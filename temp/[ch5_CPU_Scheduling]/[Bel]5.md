# Chapter 5-1. CPU 스케줄링
## Basic Concepts of CPU scheduling
- CPU scheduling is
    - the basis of multiprogrammed operating systems.
    - The objective of multiprogramming is
        - to have some processes running at all times
        - **to maximize CPU utilization**
        
### CPU scheduler
- selects a process from the processes in memory
    - that are ready to execute and allocate the CPU to that process

- Then, how can we select a next process?
    - Linked List? or Binary Tree?
    - FIFO Queue: First-In, First Out
        - 일렬로 줄 세우기
    - Priority Queue : How can we determine the priority of a process?
        - priority를 어떻게 정할지가 문제

### Preemptive(선점형) .vs. Non-Preemptive(비선점형)
- 선점형은 쫓아낼 수 있는 것
- 비선점형은 쫓아낼 수 없는 것

- Non-preemptive scheduling
    - a process keeps the CPU until it releases it,
    - either by terminating or by switching to the waiting state.

- Preemptive scheduling
    - a process can be preempted by the scheduler
    - scheduler가 어떠한 이유로 process를 쫓아냄

### Decision Making for CPU-scheduling
- 4가지 경우가 있음
1. When a process switches from the running to waiting state.
2. When a process switches from the running to ready state.
3. When a process switches from the waiting to ready state.
4. When a process terminates.

- No. 1 & 4 : no choice - non-preemptive
    - CPU가 I/O를 하니까 자발적으로 wait로 감 or terminate됨

- No. 2 & 3 : choices - preemptive or non-preemptive
    - running 상태에서 waiting 상태로 간 이유 &rarr; 쫓아낸 것
    - waiting 상태에서 ready 상태로 갔을 때 &rarr; 우선순위가 높으면 쫓아낼 수 있음

### The dispatcher is
- a module that gives control of the CPU's core
    - to the process selected by the CPU scheduler.
    - CPU와 context switch를 해주는 module을 dispatcher라고 함.

- The functions of dispatcher:
    - switching context from one process to another
    - switching to user mode
    - jumping to the proper location to resume the user program

### The dispatcher should be fast as possible
- since it is invoked during every context switch
- The dispatcher latency is
    - the time to stop one process and start another running
    - dispatcher latency는 가급적 짧아야 한다.

### Scheduling Criteria
- CPU utilization: to keep the CPU as busy as possible.
- Throughput: the number of processes completed per time unit.
- Turnaround time:
    - how long does it take to execute a process?
    - from the time of submission to the time of completion.
    - 실행부터 종료까지의 시간을 최소화 시켜라
    
- waiting time
    - Turnaround time보다 중요할 수도 있음
    - 어떤 process가 ready queue에서 waiting하는 시간을 최소화
    - the amount of time that a process spends waiting in the ready queue
    - the sum of periods spend waiting in the ready queue

- Response time
    - 반응속도를 빠르게(게이밍 마우스)
    - the time it takes to start responding

## Scheduling Algorithms
### CPU Scheduling Problem
- 많은 프로세스들 중에서 어떤 프로세스에게 CPU를 할당해 줄 것인가하는 문제
- decide which of the processes in the ready queue
    - is to be allocated the CPU's core

### The solutions for the scheduling problem:
- FCFS : First-Come, First-Served
- SJF : Shortest Job First(SRTF: Shortest Remaining Time First)
    - 남은 시간이 가장 짧은 것을 먼저 해줌
- RR : Round-Robin
    - process 전체의 시간을 다 할당해 주는 위의 2개와 다르게 시분할을 함.
    - ready queue를 가지고 쪼개진 시간에 inter living을 시키는 것
- Priority-based
    - RR을 쓸 때 다음 프로세스를 선택할 때 우선순위 선정하겠다는 것
- MLQ : Multi Level Queue
    - 상황에 따라 다른 level을 선택하는 multi level
- MLFQ : Multi-Level Feddback Queue
    - 피드백까지 추구
    - 현대적인 모습을 갖춤

### FCFS Scheduling
- 가장 간단한 CPU scheduling 알고리즘

- First-Come, First-Served: the simplest CPU-scheduling algorithm.
- The process that requests the CPU first
    - is allocated the CPU first
    - can be easily implemented with a FIFO queue
- 순서대로 처리해주면 아주 쉬움

### Note that
- The average waiting time under the FCFS policy
    - is generally not minimal and may vary substantially
    - if the processes' CPU-brust times vary greatly

- Preemptive or non-preemptive?
    - The FCFS scheduling algo is non-preemptive

- The perform in a dynamic situation
    - What if we have one CPU-bound and many I/O process?

- *Convoy Effect* (호송효과)
    - all the other processes wait for the one big process to get off the CPU
    - results in lower CPU and device utilization than might be possible
    - if the shorter processes were allowed to go first

### SJF scheduling
- Shortest-Job-First: shortest-next-CPU-burst-first scheduling.

- SJF associates with each process
    - the length of process's next CPU burst

- When the CPU is available,
    - assign it to the process that has the smallest next CPU burst.

- if two or more process are even.
    - break the tie with the FCFS

# Chapter 5-2. 스케줄링 알고리즘 
## Scheduling Algorithms
### Note that
- The SJF scheduling algorithm is provably optimal,
    - it gives the minimum average waiting time for a given set of processes.

- Moving a short process before a long one.
    - decreases the waiting time of the short process.
    - more than it increases the waiting time of the long process.
    - Consequently, the average waiting time decreases.

### Can you implement the SJF scheduling?
- next CPU burst time을 알 수 없어서 구현이 불가능 함.
- There is no way to know the length of the next CPU burst.

- Try to approximate the SJF scheduling:
    - We may be able to predict the length of the next CPU.
    - Pick a process with the shortest predicted CPU burst.

### How to predict the next CPU burst?
- exponential average of the measured lengths of previous CPU burst
- 지수적으로 평균을 내보자 &rarr; 과거부터 최근까지 가중치를 보기 위해서

### Note also that
- the SJF algo can be either preemptive or non-preemptive

- The choice arises:
    - when a new process arrives at the ready queue
    - while a previous process is still executing
    - 선점형이 유리함 &rarr; STF로 해보면 대기시간이 확연히 줄어서

- What if a newly arrived process is shorter than
    - what is left of the currently executing process?
    - 이론적으론 짧지만 구현하기는 쉽지 않음.

### SRTF Scheduling
- Shortest-Remaining-Time-First : Preemptive SJF scheduling
- SRTF will preempt the currently running process,
    - whereas a non-preemptive SJF will allow it to finish its CPU burst.

### RR Scheduling
- Round-Robin : preemptive FCFS with a time quantum
    - quantum 만큼만 실행하고 무조건 빠져나감

- A time quantum(or time slice) is a small unit of time.
    - generally from 10 to 100 milisec in length

- The ready queue is treated as a circular queue.
- The scheduler goes around the ready queue,
    - allocating the CPU to each process
    - for a time interval of up to 1 time quantum.

### One of two things will happen:
- The process may have a CPU burst of less than one time quantum
    - the process itself will release the CPU voluntarily
    - the scheduler will proceed to the next process in the ready queue.

- If the CPU burst is longer than one time quantum,
    - the timer will go off and will cause an interrupt to the OS
    - a context switch will be executed
    - the process will be put at the tail of the ready queue

### Note that
- The average waiting time under the RR policy is often long.
- The RR scheduling algorithm is preemptive.
    - if a process's CPU burst exceeds one time quantum,
    - that process is preempted and is put back in the ready queue.

### Priority-base Scheduling
- A priority is associated with each process,
    - and the CPU is allocated to the process with the highest priority.
    - Processes with equal priority are scheduled in FCFS order.

- Note that the SJF is a special case of the priority-based scheduling.
    - in this case, the priority is the inverse of the next CPU burst.

- We assume that low numbers represent high priority.

#### Priority scheduling can be
- either preemptive(SRTF) or non-preemptive(SJF).

#### The problem of starvation (indefinite blocking)
- a blocked process: ready to run, but waiting for the CPU.
- some low-priority processes may wait indefinitely.

#### A solution to the starvation problem is aging
- gradually increase the priority of processes
    - that wait in the system for a long time.

#### priority scheduling은 종류에 상관없이 항상 starvation 문제를 발생시키기 때문에 aging 같은 방법으로 해결 해줘야 함.

### Combine RR and Priority scheduling
- 2개를 섞어쓰자
- execute the highest-priority process and
- runs processes with the same priority using round-robin scheduling.

### Multi-Level Queue(MLQ) Scheduling
- ex
    - 게임을 하는 중에 전화/카톡 등이 왔을 때 이것들이 모두 같은 priority를 가지고 있지 않다.

- 분리된 ready queue에 각각의 priority를 부여하자.
- highest priority
    - real-time processes
    - system processes
    - interactive processes
    - batch processes
- lowest priority

### Multi-Level Feedback Queue(MLFQ) Scheduling
- 단계적으로 나중으로 갈 수록 할당을 더 많이 받음
- 거의 우리가 사용하는 실전 O/S의 CPU scheduling 알고리즘이라고 보면됨.(+multicore)

### On most modern operating systems
- it is kernel threads - not processes - that are being scheduled
    - 실제로는 thread scheduling을 이용하지만 어려우므로, process를 가지고 공부

- and user threads are managed by a thread library.
    - So, the kernel is unaware of them,
    - ultimately mapped to associated kernel threads

### Scheduling in the Real-Time Operating System
- 주어진 시간 내에 어떤 task를 완료할 수 있어야 실시간 이라고 한다.
- 반드시 priority를 가지고 있어야 함. (우선순위역전)

- Soft Realtime
    - Soft real-time systems provide no guarantee
        - as to when a critical real-time process will be scheduled/
        - guarantee only that a critical process is preferred to non critical one.
    - 처리를 빠르게 해주지만 좀 놓쳐도 괜찮다.

- Hard Realtime
    - Hard real-time systems have stricter requirements.
        - A task must be services by its deadline.
        - 어떤 task가 반드시 deadline 안에 실행되는 것

- 갑자기 달탐사요,,,?
- 우선순위 역전 때문에 애가 죽음 (로봇 얘기겠지?)