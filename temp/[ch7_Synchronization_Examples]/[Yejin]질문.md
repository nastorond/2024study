# Synchronization_Examples
- 재미가 있...있...있...겠죠?

## Classic Problems of Synchronization
### Bounded-Buffer
- producer : buffer 채우는거
- consumer : buffer 비우는거
- 왜 mutex를 1로 초기화하는 것인가?
-> 동시에 못들어가게 하기 위함이라는데...?
- producer
    - buf가 empty까지 wait
    - 여러개일때 race condition 방지를 위해 mutex
    - mutex 반납
    - full로 변경하여 consumer에게 알려줌
- consumer
    - full까지 wait
    - 여러개일때 방지 위해 mutex
    - mutex 반납
    - empty로 변경하여 producer에게 알려줌
- 대칭구조가 맞아야함 -> 어려워서 잘 사용하지 않음

### Readers-Writers
- read(select)만 하는것, read&write(update)하는거
- 두개 이상의 reader들이 shared data에 접근은 문제 생기지 않음
- write가 동시 또는 reader와 함께 shared data에 접근하면 문제 발생
- 혼돈의 카오스 발생
- first readers-writers problem
    - writer가 중요한데 reader에 밀려 기다림 발생
    - 기아 발생할 수 있음
    - 기존 동기화 툴(세마포어) 사용하지 않는 이유 : 느려서
        - reader는 CS에 동시에 진입해도 됨
        - readcount가 0일때 writer 진입가능
        - writer가 진입 못하고 대기 해야해서 기아 발생
        -> second로 사용해야하는 이유
    - mutex 2개 사용
    - **Q. 그러면 read_count == 1이 아닌 >= 1일때 여야 하는거 아닌가?**
    - scheduler가 우선권을 누구에게 부여하느냐에 따라 first(공평하게), second(writer에게)로 나뉨
- second readers-writers problem
    - writer한테 권한을 먼저 줌 -> reader는 계속 기다림
- 일반화 되어 reader-writer locks 사용하면 편함

혼돈의 카오스를 만들어보자...
내가 혼돈의 카오스인데

- buffer가 5일때 producer 1개에 consumer가 2번 실행되는 경우 생김
- buffer가 1일때도 같은 형상 생김
- p와 c가 모두 1일 경우 제대로 티키타카
- 경우의수
    - buffer가 1 or many
    - p & c 1 or many
    - but 적어도 동기화 문제는 생기지 않음
    - **Q. 동기화 문제가 안생긴건가...??? 그럼 저 문제는 무엇이지?**
    - **아 동기화 문제는 아니지 그럼 저건 뭐지?**
    - 코드 이해할 필요 없고 개념만... 알아도 괜춘

- 자바 코드 완벽하게 읽고 이해하지 못해도... 그냥 들어도 괜찮겠지?ㅜㅠ
- ***아빠 아들 돈주고 받고 숫자 변경해보는거 이해 잘 안됨***
- ***설명해줘요...***

- notifyall -> 다 ready queue에 간 후 경쟁하여 공평하게 기회 제공

- 철학자들은 왜 굶어 죽었을지 가장 재밌는 문제
- 이거 저번 퀴즈 문제 아닌가?
- race condition 발생하여 철학자끼리 싸움
- 이거 예방하기 위해 상호배제 적용
- 근데, 자원이 여러개가 있고, 복잡한 상황(several resoures에 대해)
    - deadlock, starvation 문제 해결 어떻게 할까
    - semaphore solution 진행 : 상호배제 문제 해결  
    -> 상호배제 적용하지 않앗을떄 :deadlock은 발생하지 않음  
    -> 상호배제 적용하면 : deadlock 쉽게 발생
    - 1) 철학자 숫자 줄이기
    - 2) 양쪽 젓가락이 사용가능 할때만 진행
    - 3) 비대칭 해결
        - 홀수번호는 왼 -> 오
        - 짝수번호 오 -> 왼
    - deadlock가능하지만, starvation 해결 불가
    - 비용도 너무 비쌈
    - 회피, 발생 시 detect 방향으로 진행
    - Mutex대신 Monitor Solution : 2) 양쪽 젓가락이 사용가능 할때만 진행
        - 상태 세개 : 생각, 배고픔, 먹음
        - Dining Philosopher Monitor -> pickup, putdown 
        - 상호배제, deadlock은 가능하지만 starvation 해결 못함

        - 50000일 경우 pthread 50000개 생성에서 에러 발생(deadlock아님)
        - deadlock 발생시키려면 pickup, putdown할때 lock을 걸지 않으면(think eat 시간도 조절 필요)
### thread-safe concurrent application
- concurrent application : performance는 좋지만, liveness hazard 발생
- thread-safe : concurrent하게 돌려도 문제 발생하지 않는 방법
    - transactional memory
        - atomic하게 설정
    - openMP
    - functional programming language
