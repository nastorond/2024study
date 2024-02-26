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
    