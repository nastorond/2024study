# 뮤텍스와 세마포어

mutex locks -> 2개
semaphore -> 야러개

## Mutex
- 상호배제
- critical section 보호
- 열쇠 획득, 반납 필요
- turn, flag 필요하지 않고 대신 하나의 var(열쇠)만 필요
- atomic하게 진행되어야 함 -> context switch로 인한 문제 예방을 위해
- 운영체제 kernel 만드는 사람에게 맡기자 ㅎㅎ

### Busy waiting
- critical section에 들어가기 위해 루프를 무한히 도는 문제 -> 심각한 단점
- 쓸데없이 CPU를 소모

### Spinlock
- spin 할때가 유용할 때도 존재
- core가 여러개 존재 시 
-> 왜... 좋은거지?
- context switch하는 시간을 줄일 수 있음?
- 아 context switch하지 않고 그냥 공회전하다가 풀리면 바로 들어갈 수 있어서
- peterson과 다른 점
    - deadlock과 starvation은 해결할 수 없지만 간단한 건 다 해결가능
- 상호배제 더 간단히 해결 가능

## Semaphores(신호기)
- 정수 변수
- P & V
- Semaphores를 구현하면 mutex lock 구현이 포함되어 있기 때문에 따로 구현하지 않아도 됨(Binary Semaphores)
- S를 n으로 초기화하여 진행
- 진입 여부 뿐만 아니라 순서도 중요
    - e.g. P1과 P2의 sync 맞추기
    - 초기값은 0으로 주고 signal 줄 떄 동작
- busy waiting 문제 발생
- multi core에서 spinlock해도 되지만, busy waiting이 없게 하는 방법도 존재
    - suspend itself : CPU 반납하고 ready queue에 대기
    - 이후, signal을 통해 waiting queue로 이동 (wakeup)
- 여러개 동작 시 race condition 발생
    - n개의 var가 따로 있다는 가정 필요
    - 그때, 같이 진입 가능
    - binary semaphore일 때 발생하지 않음
    - 한번에 한개씩 실행  
    **- Q. 그럼 동시에 못해서 안좋은거 아닌가?**


## Monitor
- 뮤텍스와 세마포어는 사용하기 어려움
    - e.g. timing error
    - 순서를 지키지 않으면 동시에 들어가는 에러 발생
    - signal이 먼저 오면 다같이 critical로 들어올 수 있음 -> 데이터가 깨짐
- 잘못사용할 가능성을 낮추자 -> simple synchronization tools : Monitor
- 에러가 잘 발생하지 않음
- ADT : Abstract Data Types
- 구조
    - Monitor
        - shared data
        - initialization code : 초기화 위해
        - operations : 동기화 위해
    - Monitor로 만들어진 thread들은 entry queue(wait queue)가 만들어짐

### Condition Variable
- synchronization을 도와주기 위해 필요
- condition variable에 wait(), signal() 제공

#### Java Monitor
- monitor lock -> semaphore lock 보다 더욱 본질적
- concurrency mechanism
- ***synchoronized***
    - monitor lock을 획득해야지 진입가능
    - 인스턴스 this 지정
    - 따로 acquire 등을 설정할 필요 없이 선언만으로 사용가능
    - 나머지는 java에서 알아서 동작 진행
- ***wait() & notify()***
    - synchoronized 선언만으로는 mutex는 해결가능하지만, 동기화는 불가능
    - 순서를 배정해주려면 wait, notify 필요
    - **Q. 그럼 synchoronized 선언만으로 사용가능하다는 장점이 없지 않나?? 짜피 wait, notify 설정해줄거면?**
    - java.lang.Object에 존재
    - 모니터락을 획득하기 위해 wait
    - 객체의 notify 호출을 통해 대기중인 쓰레드 하나 깨움
        -> notify 대신 notifyAll을 통해 전부 깨움
        -> 전부 ready queue에 갔다가 하나만 모니터락 획득 후 나머지 전부 다시 wait queue로 다시 이동
    - 매소드 전체를 묶는거 보다 임계영역만 묶는게 효율적
    - 멀티쓰레딩의 장점이 다 사라질 수 있기 때문
    - this -> 객체가 달라져서 thread 간의 서로 동기화 되지 않음
        -> for문 돌기 전에 객체 참조 변수 1개 만듦 -> thread는 5개지만, 같은 count라는 객체를 가짐
        -> this가 하나의 count를 가르킴
    - **Q. code에서 wait와 notify가 어디에 있는거지?**

## Liveness
- 위의 3개는 상호 배제만 해결(progress, bounded-waiting 해결 불가)
- deadlock, 관점에서만 보자
### DEADLOCK
- waiting process에 있는것만 이벤트를 일으킬 수 있다면 무한히 기다려야 할 수 있음
- Q와 S가 꼬여버림
### Proiorty Inversion
- 우선순위 역전현상
- 우선순위를 잠시 변경해주어 해결

