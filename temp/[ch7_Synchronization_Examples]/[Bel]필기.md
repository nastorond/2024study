- producer-consumer problem
  - producer가 계속 생산해내고 consumer는 계속 소비
  - 공유된 자원을 통해서 data를 주고 받음
  - producer는 버퍼를 가득 채우는 것이 목표
  - consumer는 버퍼를 계속 비워주는 것이 목표 <br>
<img src="./img/producer_structure.png" width="30%">
<img src="./img/consumer_structure.png" width="30%">

  - shared data Structures
    - binary semaphore mutex
      - 버퍼 pool에 접근할 때 동시에 접하면 mutual exclusion 발생
      - 1로 초기화
    - counting semaphores empty and full
      - empty / full 버퍼의 개수를 세기위해서 사용
      - empty는 n으로 설정하고 감소시킴
      - full은 0으로 설정하고 증가시킴

- Readers-Writers Problem
  - 대부분 shared-data에는 read와 write에 포함되어 있지만 RW problem은 다름
  - 여러개의 프로세스 들이 어떤 건 읽기만 하고 어떤건 둘 다 함.
    - DB에서 select, update 등

  - 만약 2개 이상의 reader가 동시에 접근한다고 했을 때는 문제가 되지 않는다.
  - 근데 2개 이상의 writer 혹은 writer와 reader가 동시에 접근하면 문제가 발생한다.

### Some Variations of the Readers-Writers Problem
- 우선 순위와 관련된 개념들
- 첫 번째 문제
  - reader는 writer가 write 하고있다고 해서 무조건 wait하면 안된다.
- 두 번째 문제