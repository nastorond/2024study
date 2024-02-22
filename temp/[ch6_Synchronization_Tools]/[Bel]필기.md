# Chapter 6-1. Synchronization Tools
## Background
### Cooperating process
- 서로 영향을 주고받는 프로세스들간의 관계
  - 주로 쓰레드나 공유 데이터

  - concurrent한 데이터가 접근할 때 데이터가 깨지는걸 염두

  - process들이 동시 접근할 때 순서를 보장해줘야 공유된 데이터들이 data consistency를 유지할 수 있음

### 여러 개의 프로세스들이 데이터의 통일성을 공유하고 있을 때
- concurrent 실행
  - 프로세스가 언제 어디서나 interrupt가 발생하면 instruction stream이 언제 어디서 끊길지 모름
  - 다음 프로세스가 들어와서 자기 프로세스를 실행시킬 때 공유 데이터에서 실행하기 때문에 문제가 발생할 수 있음

- parallel 실행
  - 여러 개의 프로세스가 분리된 CPU에서 동시에 실행되면 문제가 발생함

#### 데이터를 공유할 때 문제 예시
- 두개의 프로세스가 공유 데이터를 통해서 데이터를 공유할 때 asynchronous하게 동작함. (쓰고싶을 때 쓰고 읽고싶을 때 읽고)

- buffer의 item 개수를 세어보자.
  - 처음엔 item이 없으니 0으로 두고
  - item을 추가할 때는 increment 
  - item을 제거할 때는 decrement

```C
// item increment
while (true) {
  /* produce an item in next_produced */
  while (count == BUFFER_SIZE); 
    /* do nothing */
  
  buffer[in] = next_produced; 
  in = (in + 1) % BUFFER_SIZE; 
  count++;
}


// item decrement
while (true) {

  while (count == 0)
  ; /* do nothing */

  next_consumed = buffer[out];
  out = (out + 1) % BUFFER_SIZE;
  count--;
/* consume the item in next_consumed */
}
```

  - 예를 들어 p와 q의 process가 하나의 shared data를 공유한다고 했을 때 parallel하게 실행된다면 당연히 문제될 것 이다.
  - 그러면 concurrent하면 동시 시간 대 실행이 아닌데 왜 문제가 될까

### Data inconsistency
- 2개의 process가 분리되어 실행되고 있으니 정상적으로 동작할거라 생각하는데 생각한 대로 정상적으로 동작하지 않는다.

- count가 5라고 생각했을 때 count가 ++되고 count가 - 된다고 했을 때 생각해보면 5가 나올 것 같지만 4, 5, 6 이 랜덤하게 나온다.

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

// Case 2
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
  pthread_create(&tid1, NULL, run1, NULL);  // 이번에는 run을 1과 2로 분리
  pthread_create(&tid2, NULL, run2, NULL); 
  pthread_join(tid1, NULL);
  pthread_join(tid2, NULL);
  printf("%d\n", sum);
}
```
- 왜 이런일이 벌어질까
  - 'count++', 'count--'가 두 개의 register에서 실행될 때를 예로 들었을 때
    - count++
      1. register1에 count값을 등록
      2. register1 값을 1 증가
      3. 다시 count 값에 register1을 할당
    - count--
      1. register2에 count값을 등록
      2. register2 값을 1 감소
      3. 다시 count 값에 register2를 할당
  - 이 중에서 1, 2, 3 어디에서도 context switch가 발생할 수 있음
  - 두 개의 register는 physical 적으로는 같은 register지만 concurrent하게 실행되면 interrupt handler(scheduler)가 register 정보를 save하고 restore하는 context switch 과정에서 꼬일 수 있음 
- 즉, producer, consumer 두 개의 instruction이 어떤 순서로 sequential하게 실행되냐에 따라서 임의적인 순서로 나올 수 있다.

### **Race Condition(경쟁 상황)**
- 여러 개의 프로세스/쓰레드가 어떤 데이터를 공유하고 있을 때 이 데이터를 concurrent하게 실행시키려 할 때 어떤 순서로 일어나냐에 따라 outcome이 달라진다는 것

#### Race condition 해결법
- 어떠한 특정 시간에 1개의 프로세스만 shared data를 다루게 한다
  - process/thread synchronization이라고 함.

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

#### Race condition 연습문제
- 계좌 이체 (입금과 출금이 동시에 일어남)
  - 내가 출금을 하고 아들한테 입금이 된다고 하면 2개가 동시에 일어난다고 해야 의미가 있음
  - 이런 경우에도 race condition으로 다뤄야 함.
  - 나중에 가서 deadlock에서 다시 다룰 예정

- push/pop
  - push/pop하는 과정에서도 race condition이 일어난다.
  - 어떤 data가 race condition을 가지고 있는가 고민하보셈

## The Critical Section Problem
### The Critical Section Problem(임계 영역 문제)
- 임계 영역
  - n개의 process가 있을 때, 어떠한 코드 영역을 **critical section** 이라고 부름
  - 어떤 process에서 shared data를 update or access 한다고 할 때 이것이 다른 프로세스랑 공유가 되어있다면 critical section이라 부른다.
  - 하나의 process가 critical section을 이용하고 있을 때 다른 process가 진입, 이용할 수 없게 만들면 race condition이 안 일어나지 않을까

### The critical-section problem
- 어떤 2개의 process가 같은 시간에 실행하지 않는다 가정하면 process들의 synchronizatino이 자연스럽게 일어난다.