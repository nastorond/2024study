## CPU Scheduling
multiprogrammed operating systems의 기본

#### multiprogramming의 목적  
&rarr; CPU utilization 최대화  
&rarr; context switch를 통해 time sharing하여 concurrently 실행  
&rarr; some processes running at all times

<img src="./img/CPU_bursts&IO_bursts.png" height = 300px><br>
CPU burst : running 상태   
I/O burst : ready 또는 waiting 상태   
&rarr; CPU burst time이 많은 process보다, I/O burst time이 많은 process가 많음  
&rarr; CPU Scheduling을 통해 time-sharing 했을 때, 효율이 좋아지는 이유   

### CPU scheduler
메모리에 load 되어있는 <b>ready</b> 상태의 processes 중 CPU에게 어떤 process를 <b>allocates</b> 할 것인지 선택  
#### ready queue를 만드는 방법(next process 선택 방법)
&rarr; Linked List or Binary Tree로 구현 가능  
&rarr; <b>FIFO Queue</b>(First-In, First-Out)  
&rarr; <b>Priority Queue</b> : priority 부여 방법 중요

### Preemptive(선점형) vs Non-preemptive(비선점형)
#### Non-preemptive scheduling &rarr; 자발적
process가 CPU를 선점 시 releases 되기 전까지 유지  
a process keeps the CPU until it releases it,  
either by terminating or by switching to the waiting state.  

#### Preemptive scheduling &rarr; 강제적
scheduler가 CPU를 점유하고 있는 process를 쫒아낼 수 있음
a process can be preempted by the scheduler.

### CPU-scheduling을 위한 의사결정
1. When a process switches from the <b>running</b> to <b>waiting</b> state.  
2. When a process switches from the <b>running</b> to <b>ready</b> state.  
3. When a process switches from the <b>waiting</b> to <b>ready</b> state.  
4. When a process <b>terminates</b>.  

&rarr; 1 & 4: no choice – non-preemptive  
&rarr; 2 & 3: choices – preemptive or non-preemptive  
&rarr; 현대적 O/S에서는 선택권이 넓은 preemptive 사용

### Dispatcher
context switch를 하는 module  
&rarr; CPU scheduler에 의해 선택된 process에게 control of the CPU’s core 제공  
&rarr; 모든 context switch에 호출되기 떄문에 ASAP  

#### dispatcher 기능
&rarr; switching context from one process to another  
&rarr; switching to user mode  
&rarr; jumping to the proper location to resume the user program  

Tip)  
CPU scheduler : 어떤 process를 변경할지 선택  
Dispatcher : 실제로 변경해주는 것(큰 의미로는 CPU scheduler에 포함)  

#### dispatcher latency(dispatcher 진행시간)

<img src="./img/DispatcherRole.png" height = 300px><br>
P0와 P1이 context switch 진행 시  
PCB0를 store하고, PCB1를 restore하는 시간  
&rarr; the time to stop one process and start another running  
&rarr; 가급적이면 짧아야함

#### vmstat
context switch가 얼마나 자주 일어나는지 확인하는 명령어  
<img src="./img/vmstat.png" height = 150px><br>
위의 사진에서는  
<b>vmstat 1 3(1초씩 3번 검사 진행)</b>  
&rarr; 1초에 context switch가 5, 30, 27번 진행  
<b>cat /proc/1/status/grep ctxt</b>  
&rarr; voluntary_ctxt_switches(자발적 : non-Preemptive): 62  
&rarr; nonvoluntary_ctxt_switches(비자발적 : Preemptive): 0  

## Scheduling Criteria
<b>CPU utilization</b>  
&rarr; to keep the CPU as busy as possible  

<b>Throughput</b>  
&rarr; 단위 시간 내의 완결된 process의 수  
&rarr; the number of processes completed per time unit.  

<b>Turnaround time⭐️</b>  
&rarr; 제출(실행)에서 종료까지의 시간  
&rarr; how long does it take to execute a process?  
&rarr; from the time of submission to the time of completion.  

<b>Waiting time⭐️⭐️</b>  
&rarr; ready queue에서 대기 시간의 합 최소화  
&rarr; the amount of time that a process spends waiting in the ready queue.  
&rarr; the sum of periods spend waiting in the ready queue.  

<b>Response time</b>    
&rarr; response 시간 최소화  
&rarr; UI 측면에서 중요  
&rarr; the time it takes to start responding  

## Scheduling Algorithms
#### CPU Scheduling Problem
• decide which of the processes in the ready queue
- is to be allocated the CPU’s core.

 The solutions for the scheduling problem:
• FCFS: First-Come, First-Served
• SJF: Shortest Job First (SRTF: Shortest Remaining Time First)
• RR: Round-Robin
• Priority-based
• MLQ: Multi-Level Queue
• MLFQ: Multi-Level Feedback Queue