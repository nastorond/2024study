# Chapter 6-1. Synchronization Tools
- 오해하고 있던 사실
```java
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
guarantee가 없다 = 아무것도 장담할 수 없어. 보장할 수 없어. 약속할 수 없어.

여기서 producer가 p0 고 consumer가 p1
그래서 flag[0]는 producer가 사용하고 싶어서 buffer에 대기를 거는거고
turn = 1 이 consumer에게 사용권한을 주는 것 (사실상 서로 먼저 이용하라고 양보하는 것과 같음)

그래서 flag가 1이고 turn이 consumer 차례일 때는 대기하고 있다가 consumer가 다 사용하고 나서 flag를 넘겨주면 빠져나오는 것

1. 두 프로세스 모두 flag를 true로 설정하고 다른 프로세스의 flag와 turn을 확인한다고 했을 때 두 프로세스 모두 조건을 충족한다고 판단하면 두 프로세스가 동시에 
critical section으로 진행하여 데이터 불일치가 발생 = 상호 배제가 안됨 = race condition 발생

2. 특정 상황에서 두 프로세스 모두 서로가 critical section을 완료할 때까지 무한 루프에 갇힐 수 있음.


애초에 두 프로세스가 동시에 critical section에 들어가면 프로세스가 parallel 하게 실행되는 것과 같기 때문에 문제가 발생함.

# Chapter 6-2. Synchronization Tools 2
## 