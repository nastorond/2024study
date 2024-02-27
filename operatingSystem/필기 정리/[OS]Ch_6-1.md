# Chapter 6-1. Synchronization Tools

## Background

### Cooperating process
- 서로 영향을 주고받는 관계
- can share a logical address space or be allowed to share data
- However, **concurrent access** to shared data may result in ***data inconsistency***
- process들이 동시 접근할 때 순서를 보장해줘야 공유된 데이터들이 data consistency를 유지할 수 있음

### The inegritty of data shared by several processes(or thread)
- **concurrent 실행**
  - 프로세스가 instruction stream의 임의의 지점에서 중단될 수 있음
  - 다음 프로세스가 들어와서 자기 프로세스를 실행시킬 때 공유 데이터에서 실행하기 때문에 문제가 발생할 수 있음

- **parallel 실행**
  - 여러 개의 프로세스가 분리된 CPU에서 동시에 실행되면 문제가 발생

### 데이터를 공유할 때 문제 예시
- producer-consumer problem
  - where two processes _share data_ and are _running asynchronously_

  - buffer의 item 개수 계산
    - initialized : 0,
    - add a new item : increment,
    - remove one item : decrement

    ```C
    // item increment
    while (true) {
        /* produce an item in next_produced */

        while (count == BUFFER_SIZE); /* do nothing */
        
        buffer[in] = next_produced; 
        in = (in + 1) % BUFFER_SIZE; 
        count++;
    }


    // item decrement
    while (true) {

        while (count == 0); /* do nothing */

        next_consumed = buffer[out];
        out = (out + 1) % BUFFER_SIZE;
        count--;

        /* consume the item in next_consumed */
    }

### Data inconsistency
- 2개의 process가 분리되어 실행되고 있으니 정상적으로 동작할거라 생각하는데 생각한 대로 정상적으로 동작하지 않음
  - 두 가지 상황 ( count++; & count--; )에서 producer and consumer가 concurrently execute 할 떄, count는 5라고 예상
  - 하지만, count 값은 4, 5, 6 다양하게 나옴

    ```C
    // Case 1
    #include <stdio.h> 
    #include <pthread.h>

    int sum;

    void *run(void *param)
    {
        int i;
        for (i = 0; i < 10000; i++)
            sum++; 
        pthread_exit(0);
    }

    int main() 
    {
        pthread_t tidl, tid2;
        pthread_create(&tid1, NULL, run, NULL);
        pthread_create(&tid2, NULL, run, NULL); 
        pthread_join(tid1, NULL);
        pthread_join(tid2, NULL);
        printf("%d\n", sum);
        // 생각대로라면 tid1, tid2가 10000씩 증가시키기 때문에 20000이 나올것이다.
        // 실제 출력값 : 19099, 19385, 20000, 20000, ...
    }

    // Case 2

    int sum;

    void *run1(void *param)
    {
        int i;
        for (i = 0; i < 10000; i++)
            sum++; 
        pthread_exit(0);
    }

    void *run2(void *param)
    {
        int i;
        for (i = 0; i < 10000; i++)
            sum--; 
        pthread_exit(0);
    }

    int main() 
    {
        pthread_t tidl, tid2;
        pthread_create(&tid1, NULL, run1, NULL);  // 이번에는 run을 1과 2로 분리
        pthread_create(&tid2, NULL, run2, NULL); 
        pthread_join(tid1, NULL);
        pthread_join(tid2, NULL);
        printf("%d\n", sum);
    }
    ```
  #### 왜 이런일이 발생할까?
  - 한 문장으로 생각되었던 _count++, count--_ 은 사실 기계어로 3문장으로 나뉘어 있음
    ```c
        register = count
        register = register + 1
        count = register

        register = count
        register = register - 1
        count = register
    ```
  - 위의 register는 physical 적으로는 같은 register지만, <br>
    concurrent하게 실행되면 interrupt handler(scheduler)가 register 정보를 ***save***하고 ***restore***하는 context switch 과정에서 꼬일 수 있음 
  - The concurrent execution of count++ and count--
    - is equivalent to a sequential execution
    - in which the lower-level statements presented previously
    - are _interleaved_ in some _arbitrary order_<br>
    <img src="./img/howmanyhapend.png" width=""><br>

### **_Race Condition_** (경쟁상황, 경쟁상태)
- A situation 
    - where several processes (or threads) 
    - access and manipulate the _same_ (or _shared_) _data concurrently_
    - and the outcome of the execution 
    - depends on the _particular order_ in which the access takes place
- 즉, 공유 데이터를 concurrent하게 실행 시킬 때, 순서에 따라 outcome이 달라지는 현상

### To guard against the race condition
- We need to ensure that
  - only one process at a time can manipulate the shared data(e.g. the variable cnt)
- To make such a guarantee
  - we require that the processes are _synchronized_ in some way
  - to say, _process_(or _thread_) _synchronization_
- 경쟁상황을 막기 위해 특정 시간에 1개의 프로세스만 shared data를 다루게 하는 것

```Java
// Race condition 해결
public class RaceCondition1 {

  public static void main(String[] args) throws Exception {
  RunnableOne run1 = new RunnableOne();
  RunnableOne run2 = new RunnableOne();
  Thread t1 = new Thread (run1); 
  Thread t2 = new Thread (run2);
  t1.start(); t2.start();
  t1.join(); t2.join();
  System.out.printIn("Result: " + run1.count + ", "+ run2.count);
  }
}

class RunnableOne implements Runnable {

  int count = 0;

  @Override
  public void run() {
    for (int i = 0; i < 10000; i++)
      count++;
  }
}

// Race condition 발생
public class RaceCondition2 {

  public static void main(String[] args) throws Exception {
  RunnableTwo run1 = new RunnableTwo();
  RunnableTwo run2 = new RunnableTwo();
  Thread t1 = new Thread (run1); 
  Thread t2 = new Thread (run2);
  t1.start(); t2.start();
  t1.join(); t2.join();
  System.out.printIn("Result: " + RunnableTwo.count);
  }
}

class RunnableTwo implements Runnable {

  static int count = 0; // static 변수 사용

  @Override
  public void run() {
    for (int i = 0; i < 10000; i++)
      count++;
  }
}
```

#### Race condition 예시
- 계좌 이체 (입금과 출금이 동시에 일어남)
  - 내가 출금을 하고 아들한테 입금이 된다고 하면 2개가 동시에 일어난다고 해야 의미가 있음
  - 이런 경우에도 race condition으로 다뤄야 함

- push/pop
  - push/pop하는 과정에서도 race condition이 일어남

## The Critical Section Problem

### The Critical Section Problem : 임계 영역 문제
- Consider a system consisting of 𝑛 processes {𝑃0,𝑃1,⋯,𝑃𝑛−1}
    - Each process has _a segment of code_, called a ***critical section***
    - in which the process may be _accessing_ and _updating_ data
    - that is **_shared with_** at least one other process
- The important feature of the system is that,
    - when one process is executing in its critical section,
    - no other process is allowed to execute in its critical section.

### The **_critical-section prob_**
- 어떤 2개의 process가 같은 시간에 실행하지 않는다 가정 시
- To design a protocol that
  - the processes can use to **_synchronize_** their activity
  - so as to _cooperatively share_ data

### Sections of codes
- source code 영역을 4가지로 나눌 수 있음<br>
![Sections of codes](./img/Sections_of_codes.png)<br>

- The **_entry-section_** the section of code
  - to request permission to enter its critical section
- The _critical-section_ follows the entry-sectino
    - 임계영역 -> e.g.) count ++ 하는 영역
- the **_exit-section_** follows the critical-section
    - return permission and exit after execution in critical
- The _remainder-section_ is the section of remaining code
    - non-critical section -> e.g.) count -- 하는 영역

### Three requirements for the solutions

- **_Mutual Exclusion_** 상호배재
  - If process 𝑃𝑖 is executing in its critical section
  - then no other processes can be executing in their critical section

- _Progress_ : aviod _deadlock_
  - If no process is executingg in its critical section and some processes wish to enter their critical section
  - 교차로에서 사방에서 차가 다가와 꽉 막혀 움직일 수 없는 상황
  - then the selection of next process will enter its critical section next cannot be postponed indefinitely

- _Bounded waiting_ : aviod _starvation_
  - A bound (or limit) on the number of times that other processes are allowed to enter their ciritcal sections
  - after a procces has made a request to enter its critical section and before that request is granted
  - 우선순위에서 밀려 계속 critical section에 들어가지 못하는 상황

### E.g. of race condition

<img src="./img/racecondition_ex.png" width=""><br>

### A simple solution in a single-core environment
- _Prevent interrupts_ from occurring
  - while a shared variable was being modified
  - critical section 문제를 해결하기 위해 가장 좋은 방법(단순 무식한 방법)
- We could be sure that
  - the current sequence of instructions
  - would be allowed to execute in order without preemption
- No other instructions would be run
  - so no unexpected modifications could be made to the shared data
- Unfortunately, _not feasible_ in a _multiprocessor_ environment
  - core가 여러 개 있다면 하나씩 다 막아버려야 하기 때문에 비효율적
### Two general approaches
- preemptive kernels and non-preemptive kernels

- preemptive kernel (선점형)
  - 동기화 문제가 반드시 발생하고 다루기 어려움
  - 대신 더 responsive 하기 때문에 자주 사용함(race condition 문제를 해결하고 사용할 만큼 가치 존재)
- non-preemptive kernel (비선점형)
  - 어떤 kernel 모드가 진입하면 다시 내려놓기 전까지 CPU를 쓰기 때문에 context switch가 발생하지 않음
  - 때문에, race conditions 문제가 발생하지 않음
  - 하지만, 성능이 좋지 않기 때문에 요즘엔 거의 사용하지 않음

## Peterson's Solution

### Software Sols to the Critical-Section Prob:
- Dekker's Algorithm
  - for two processes 
- Eisnberg and McGuire's Algorithm
  - for n processes with a lower bound on waiting of n - 1 turns
- Bakery Algo
  - 레슬리 램포트가 제안한 알고리즘
- ***Peterson's Algorithm***  
  &rarr; a classic software solution to the critial-section problem
  - no guarantees that Peterson’s solution will work correctly,
  - since modern computers perform basic machine-language instructions
  - such as *load* and *store*

### Peterson's solution
- restricted to two processes that alternate execution
  - between their critical sections and remainder sections
  ```c
  int turn;
  bool flag[2];

  while (ture) {
    flag[i] = true;
    turn = j;
    while (flag[j] && turn == j)
      ;
      /* critical section */
    flag[i] = false;
      /* remainder section */
  }
  ```
- 데이터가 공유되는 지점에서 충돌(race condition)이 일어날 것으로 예상될 때, entry section 과 exit section을 만들어 문제 해결
- flag를 통해 관리
- 산책을 시키려하는데 2마리의 개가 자꾸 싸우니까 flag를 꽂아서 내가 산책 나간다고 표시  
&rarr; 상대방이 산책중이면 기다렸다가 끝나면 내가 산책

### simple implementation of Peterson's solution
```c
#include <stdio.h>
#include <pthread.h>
#define true 1
#define flase 0

int sum = 0;
int turn;
int flag[2];

void *producer(void *param) {
  int k;
  for (k=0; k<10000; k++) {
    /* entry section */
    flag[0] = true;
    turn = 1;
    while (flag[1] && turn == 1)
      ;
    /* critical section */
    sum++;

    /* exit section */
    flag[0] = false;

    /* remainder section*/
  }
  pthread_exit(0);
}

void *consumer(void *param) {
  int k;
  for (k=0; k<10000; k++) {
    /* entry section */
    flag[1] = true;
    turn = 0;
    while (flag[0] && turn == 1)
      ;
    /* critical section */
    sum--;

    /* exit section */
    flag[1] = false;

    /* remainder section*/
  }
  pthread_exit(0);
}

int main() {
  pthread_t tid, tid2;
  pthread_create(&tid1, NULL, producer, NULL);
  pthread_create(&tid2, NULL, consumer, NULL);
  pthread_join(tid1, NULL);
  pthread_join(tid2, NULL);
  printf("sum= %d\n", sum);
}

```
&rarr; 0,0,0,-13,-9 ...
&rarr; 횟수를 높일수록 결과가 0으로 나오지 않고 튀는 경우가 많아짐

### What happen?
- Threre are *no guarantees* that
  - Peterson's solution _will work correctly_
  - if the architecture perform basic machine-language instructions,
  - such as **load** and **store**
- 그럼에도 Peterson's solution을 사용하는 이유
  -  algorithmic description of solving the CSP이 좋음 &rarr; 이해하기가 쉬움
  - illustrates some of the complexities involved in the requirements of _mutual exclusion_, _progress_, and _bounded waiting_

### Peterson's solution is provably correct
- **Mutual exclusion** in preserved
  - Note that each $P_i$ enters its critical section
  - only if either flag[j] == flase or turn == i
  - 동시에 크리티컬 섹션에 들어가지 않는 것을 보장
- The *progress* requiremet is satisfied(**No deadlock**)
- The *bounded-waiting* requirement is met(**No starvation**)
- 상호배제, 데드락, 기아를 해결할 수 있는 솔루션

## Hardware Support for Synchronization

### Hardware-based Solutions
-  critical-section problem 해결을 위한 하드웨어 지원 필요
  - instruction이 제공되었을 때 동기화 도구로 직접 사용하면 좋음
  - more abstract mechanisms의 기초를 형성하는 데 사용 가능

- Three primitive operations
  - ***memory barriers or fences***
  - ***hardware instructions***
  - ***atomic variables***

### Atomicity(원자성)
- Atom - 더이상 쪼갤 수 없는 물리적 성분
- An _atomic operation_ is one _uninterruptible unit_ of operation
- Modern computer system provide _special hardware instructions_
  - e.g. _atomic instructions_
  - that allow us either to _test and modify_ the content of a word
  - or to _test and swap_ the contents of two words
- instruction 자체를 atomic한 instruction으로 만들자
  - 클락을 쪼개지 말고 한방에 해결되도록 회로를 구성

### Two types of conceptual atomic instructions:
- **test_and_set()**
- **compare_and_swap()**

### test_and_set() instruction:
```c
boolean test_and_Set(boolean *target) {
  boolean rv = *target;
  *target = true;
  return rv;
}
```


- target 값을 true / false로 바꾸는 hardware instruction가 있을때,
- A global Boolean varialbe _lock_
  - is declared and initialized to _false_  
&rarr; 코드의 중간에서 interrupt가 발생하지 않음


```c
do {
while (test_and_set(&lock)) // 상호배제 가능 -> context switch가 안 일어남 
    ; /* do nothing */

    /* critical section */

lock = false;

    /* remainder section */
} while (true);
```
- mutual exclusion은 확실하게 보장됨
- critical section에 동시 진입하는 일은 사라짐
- deadlock이나 starvation은 앞으로도 해결되는 solution은 없음

### The compare_and_swap() instruction:
```c
int compare_and_swap(int *value, int expected, int new_value) {
  int temp = *value;
  if (*value == expected) 
    *value = new_value;

  return temp;
}
```

- A global Inteager variable _lock_
  - is declared and initialized to 0
```c
while (true) {
  while (compare_and_swap(&lock, 0, 1) != 0)
    ; /* do nothing */

    /* critical section */

  lock = 0;

    /* remainder section */
}
```

### Atomic Variable
- compare_and_swap instruction을 _atomic variable_ 와 같은 construction other tools을 만드는 도구로 사용할 수 있음.
- An _atomic variable_ provides
  - atomic operations on basic data types such as integers and Booleans
  - can be used to ensure mutual execlusion in situations
  - where there may be a _single variable_ with _race condition_

### Java implementation of Peterson's solution

```java
public class Peterson1 {

  static int count = 0;
  
  static int turn = 0;
  static boolean[] flag = new boolean[2];

  public static void main(String[] args) throws Exception {
    Thread t1 = new Thread(new Producer());
    Thread t2 = new Thread(new Consumer());
    t1.start(); t2.start();
    t1.join(); t2.join();
    System.out.printIn(Peterson1.count);
  }
}

static class Producer implements Runnable {
  @Override
  public void run() {
    for (int k = 0; k < 10000; k++) {
      /* entry section */
      flag[0] = true;
      turn = 1;
      while (flag[1] && turn == 1)
        ;

      /* critical section */
      count++;
      
      /* exit section */
      flag[0] = false;

      /* remainder section */
      }
    }
  }

static class Consumer implements Runnable {
  @Override
  public void run() {
    for (int k = 0; k < 10000; k++) {
      /* entry section */
      flag[1] = true;
      turn = 0;
      while (flag[0] && turn == 0)
        ;

      /* critical section */
      count--;
      
      /* exit section */
      flag[1] = false;

      /* remainder section */
    }
  }
}
```
- -1,-1,-1,2,2,2,0,0 ...
- 10만번 실행 시, 데드락 걸려서 결과가 나오지 않음
- entry section에서 context switch가 일어날 때 race condition이 같이 발생해서 오류가 발생
- java에서 제공하는 AtomicBoolean 함수를 통해 버그 해결

```java
import java.util.concurrent.atomic.AtomicBoolean;

public class Peterson2 {

  static int count = 0;
  
  static int turn = 0;
  static AtomicBoolean[] flag;
  static {
    flag = new AtomicBoolean[2];
    for (int i = 0; i < flag.length; i++)
      flag[i] = new AtomicBoolean();
  }

}

static class Producer implements Runnable {
  @Override
  public void run() {
    for (int k = 0; k < 10000; k++) {
      /* entry section */
      flag[0].set(true);
      turn = 1;
      while (flag[1].get() && turn == 1)
        ;
        
      /* critical section */
      count++;
      
      /* exit section */
      flag[0].set(false);

      /* remainder section */
    }
  }
}

static class Consumer implements Runnable {
  @Override
  public void run() {
    for (int k=0; k< 10000; k++) {
      /* entry section */
      flag[1].set(true);
      turn = 0;
      while (flag.get() && turn == 0)
        ;
      /* critical section */
      count--;
      
      /* exit section */
      flag[1].set(false);

      /* remainder section */
    }
  }
}
```
- 0, 0, 0, 0, 0, 0, ...
- 오류가 안나고 거의 같은 값이 나옴

### 마무리
- race condition : 공유된 자원(shared data)에 여러 프로세스가 엑세스 하려할 때
    - 공유된 자원의 영역 : critical-section
    - 보호하기 위해 : Synchronization problem
    - 진입을 위한 : entry-section
    - 나오기 위한 : exit-section
    - non-critical section : remainder-section
- 해결하기 위한 가장 기본적인 방법
    - 상호배제
    - progress, 데드락 방지
    - bounded waiting 한정적 대기, 기아 방지
- Peterson's solution
    - 상호배제 해결 방법 :
        - 뮤텍스
        - 세마토어
        - 모니터