## 동시성(Concurrency)과 병렬성(Parallelism)의 차이
### Concurrency
javascript의 thread : single-thread 지원
즉, 하나의 thread에서 번갈아가며 처리

### Parallelism
java의 thread : multi-thread 지원
즉, 여러 thread에서 각각 처리

## 왜 원하는 값이 나오지 않는가?
한 문장으로 생각되었던 count++, count--은 사실 3문장으로 나뉘어 있음
```c
register = count
register = register + 1
count = register

register = count
register = register - 1
count = register
```

따라서 context switch가 발생할 수 있는 순간은 3군데 모두  
처음이나, 마지막에 발생하면 문제가 없지만,  
중간에 진행되면 문제 발생  
즉, instruction의 어디에서 context switch가 발생하느냐에 따라 save, restore 시 문제 발생 가능  

### 경쟁 상황
순서에 따라 결과가 달라지는 문제점   
순차적 실행되도록 동기화 진행  
Q. 그럼 번갈아 진행 안하면 concurrency가 없어지는 것 아닌가?  
Q. static으로 변수 선언 시 class 변수가 된다?  
-> 공유된 변수이기 때문에  
-> 따라서 문제 발생  
Q. 그럼 static이 아니면 따로 count가 각각 존재하는거고, static이면 두 개에서 하나의 변수를 공유하는 것인가?  

### 임계 영역
critical section : 다른 프로세스와 공유되고 있을 때  
진행 되고 있을 때 진입을 막자  
Q. 그러면!! concurrency가 없어지는거 아니냐니까?  
동기적이면 비동기로 일어나지 못하니까 안되는거 아닌가?  

### solution
- Mutual Exclusion(상호배제)
Q. deadlock, starvation 상황은 어떤 상황인가?  
- Progress : avoid deadlock  
-> Q. 이게 다 들어와있어 아무것도 처리하지 못하는 상황?  
- Bounded Waiting : avoid starvation  
-> Q. 이게 너도 나도 들어오고 싶어 결국 아무도 못들어오는 문제?

pid가 같으면 공유되어 있는 상황  
따라서 공유된 변수를 사용하기 때문에 문제 발생 확률 높아짐

### The Critical Section Problem
#### single-core
-> multi process의 장점이 사라짐

- Non-preemptive kernel
    - 자신이 내려놓기 전까지 선점 가능 -> race condition 발생 x
    - but, 성능이 느려 사용하지 않음
- Preemptive kernel
    - race condition이 발생하지만, 이 문제를 해결하고 사용할 만큼 가치 존재

### 결론 : data integrity를 위해 Synchronization 사용

## Synchronization Solution
### Software Solutions
- Dekker's
- Eisenberg and McGuire's
- Bakery
- Peterson's
    - race condition이 발생하지 않도록 entry section과 exit section 제공
    - Q. turn을 왜 entry section 후에 주지 않고, 전에 주는 걸까?
    - Q. 둘 다 안돌아가는거 아니야?

    - 실패 발생
    - 이유 : guarantees가 없음? guarantees가 뭔데??
    - 일단 while 들어가기 전에 context switch가 발생해서 문제가 발생할 수 있다는건 알겠는데...
    - Mutual exclusion, No deeadlock, No starvation 증명가능
    - but, hardware-base가 필요
    - 3줄로 나누지 말고 one-clock에 해결하도록 진행 : Automic -> 명령어로 지정(단위 operation)
    - 아직 Mutual exclusion은 해결 가능한데 No deeadlock, No starvation까지는 해결 불가

