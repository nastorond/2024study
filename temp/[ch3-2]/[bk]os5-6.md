# Process간 통신 (IPC)

- concurrently executed process 는 2가지 경우가 있다.
  - independent processes
    - 공유하는 데이터 없음
  - cooperating processes
    - 다른 process에게 영향을 주거나 영향을 받는다.
    - 데이터를 공유하거나 메세지를 주고받는 경우

    &rarr; 해결 방법 : Interprocess Communication (IPC)

## Interprocess Communication (IPC)

- cooperating process는 서로 데이터를 exchange(send, receive)하는데 이 때 IPC mechanism 이 필요하다.  

- 두 가지 모델이 있다. 둘 중 한 방법으로 통신을 한다. 
  - Shared memory (공유 메모리)
    - Process 사이 공간에 shared memory 공간이 존재해서 데이터 공유
  - Message passing 
    - o/s에게 맡기는 방법
    - 별도의 message queue를 통해서 message를 주고 받는다. 
    - kernel(O/S kernel)이 개입한다.

### IPC - Shared-memory systems

- Producer-consumer Problem (cooperating processes simile)
  : Producer produces information that is consumed by a consumer
  - compiler & assembler (data : assembly code)
  - web server & browser (data : HTML file)

- Solution using shared-memory :
  - allow produce and consumer to run concurrently
  - CPU를 timesharing하며 context switch하며 concurrently 하게 실행
  - **buffer**를 사용
    - producer : fill the buffer
    - consumer : empty the buffer
  - bounded buffer 
    - if buffer is full
      - produce 가 wait
    - if buffer is empty
      - consumer 가 wait

  &rarr; buffer = shared memory
  - shared memory : producer and consumer process가 공유하는 메모리 영역
    - O/S가 관리해줌 

    


