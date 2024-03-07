# 데드락(교착상태)의 이해
가장어려운 파트...
아니...이번주 정리 에반데?????? 이거 필기 언제 합쳐....
강의 겁나 어려운데 겁나 기네

## system model
- 데드락 : 트리거조차 wait 상태에 있어서 ready로 가지 못하는 상태
- waiting thread가 자신의 상태를 변경하지 못함
    - 리소스가 다른 wait에 의해 점유되어 있기 때문
- resource type는 identical insrances들로 구성되어 있음
    - e.g. cpu cycles -> core 갯수만큼 동시 공유 가능
    - request -> use(CS) -> release

- 잘못한 사람이 없지만 협의 안했으니 모두 잘못
- 철학자 저녁 식사에서 젓가락하나로 신경전 벌이고 있는 상태 -> deadlock이 걸리기 쉬운 상태

### necessary condition
- Mutual Exclusion
    - 읽기만 한다면 : sharable
    - write가 있다면 : race condition 발생 -> data inconsistency -> data integrity 깨짐 -> 따라서 상호배제 필요
    - 상호배제가 일어나지 않는다면 deadlock 일어나지 않음
- hold and wait
- no preemption : 선점 불가
- circular wait
- 억지로 예제를 만들지 않는다면 deadlock에 걸리기 쉽지 않음

### RAG : 자원 할당 그래프
- V : vertices
    - T : active thread
    - R : resource type
    - T -> R : request edge
    - R -> T : assignment edge
    - 사이클이 생기면 deadlock 걸릴 수 있음
    - ***사이클이 있어도 deadlock이 걸리지 않을 수 있음 왜지?***
    - 뭐야 안발생한다며? 거꾸로 이야기했다고?ㅋㅋㅋ 
- E : edges  

### 3가지 방법
- 무시(기도) 메타
- Deadlock Prevention 완전 방지 -> 위성 제어 등과 같이 매우 비싼 시스템아니면 거의 불가능
    - Deadlock Avoidance : 완전 방지 말고 피하는 방법 -> 사실 이것도 부담스러움
- detect : 발생했을때 recover

### Deadlock Prevention
- 4개 필요 요건 중 하나만 막으면 되지 않을까?
- mutual exclusion
    - 모든 리소스가 sharable하게 만들기
    - 하지만, 이런 경우는 있을 수 없음
    - mutex lock을 공유할 수는 없음
    - 따라서 이건 사용 불가 방법
- Hold and wait
    - hold하지 맑고 나에게 기회가 왔을떄 hold
    - 실용적이지 않음
- No preemption
    - 위에 꺼는 나의 차례가 올때까지 모든 걸 내려놓는 방식
    - 이 방식은 내가 뺏는 방식 (우선순위)
    - 문제 발생할 수 있어 사용할 수 없음
- Circular wait
    - 그나마 괜찮은 방법
    - 자원 타입에 순서 부여
    - 내가 점유하고 있는 리소스보다 큰 순서만 요청 가능
    - 따라서 다른 나보다 높은 애가 요청하면 나는 놓을 수 밖에 없음
    - 때문에 deadlock 걸리지 않음
    - 그러나... 이거 조차도 deadlock을 완전 막을 수 없음
    - transaction(계좌이체)
        - 아빠 계좌에서 아들 계좌로 transaction하게 실행
        - 두 개의 lock을 모두 획득한 상태에서만 withdraw, deposit (돈 입출금) 가능
        - 문제는 transaction(checking_account, saving_account, 25.0), transaction(checking_account, saving_account, 25.0)이 동시에 요청 될 떄 발생
        - grandlock을 사용하여 방지, monitor lock 사용하여 방지 등의 방법은 있지만 결론은 잘 안됨
        - 그냥 prevent 말고 avoid 하자

# 데드락과 뱅커 알고리즘
## Deadlock Prevention의 단점
- 4개 중에 한가지 막아보려했는데
    - 부작용 상당히 많음 -> low device utilization, reduced system throughput
    - 멀티쓰레딩으로 얻은 장점을 다 까먹어버림

### Deadlock Avoidance
- Request를 받아주기 전에 future deadlock 고려 -> 존재하면 wait
- 추가적 정보 필요 : 리소스가 어떻게 요청들어오는지 알아야함
    - P가 R1을 쥐고 있는 상태에서 R2 요청
    - Q가 R2를 쥐고 있는 상태에서 R1 요청
    - 바로 deadlock
    - 따라서 Q가 R1을 request할때 deadlock 여부 확인 후 진행 필요
    - 말은 간단하지만 사실 어려운 작업
- priori information이 주어졌다면, 
    - 알고리즘을 활용해 deadlock에 들어가지 막아보자
- 쓰레드가 요구하는 최대 갯수를 알고 있으면 좋음
- 리소스의 갯수 분류 -> available, allocated 상태, maximum demands

#### safe state
- maximum까지 allocate 가능한 실행 순서 찾기가 핵심
- safe에서 unsafe로 넘어가는 것을 막음
- avoid algorithm
    - single instance
    - multiple instance
    - ***근데 나 사실 instance 개념이 잘 없음...***
    - 시스템 초기 상태 -> safe state
    - 그 이후의 request에 대해 항상 deadlock 발생 가능 여부 판단
    - claim edge 준다음 grant를 받으면 request 실행

### banker's algorithm
- RAG보다 덜 효율적이고, 복잡하지만, multiple instance에서 사용 가능
- 은행은 고객이 deadlock 상태에 빠지지 않게 해줘야함
- Data structures
    - thread : n개
    - resource : m개
    - Available : available resource types을 가지고 있는 벡터
    - Max : 각 thread가 요청할 최대 resource instance 수
    - Allocation : currently allocated
    - Need : remaining resource

    - Available[m]
        - Available[j] == k -> k개의 instances를 available

    - Max[n x m]
        - Max[i][j] == k -> Ti가 기껏해야 k개 요청 할 것
    - Allocation[n x m]
        - Allocation[i][j] == k -> Ti가 Rj의 k개 인스턴스를 allocated
    - Need[n x m]
        - Need[i][j] == k -> Ti가 Rj의 k개 더 요청할 것
        - Max[i][j] - Allocation[i][j]
        - Need가 있으면 굳이 Max값이 필요하지 않음

### Safety algorithm
1. work, finish 초기화
    - work : (3, 3, 2) -> avaliable로 초기화 진행
    - ***Q. work가 뭐지...?***
    - Finish : false, false, false, false, false
2. Finish == false && Need <= Work
    - i가 0일 때,
        - Need : 7, 4, 3
        - Work : 3, 3, 2
        - 안됨
    - i가 1일 때, 
        - Need : 1, 2, 2
        - Work : 3, 3, 2
        - 가능 
        - work = work(3, 3, 2) + allocation(2, 0, 0)
        - ***Q. work가 뭔데 allocation을 더하는거지?***
        - work : 5, 3, 2
        - T1의 finish True로 변경
    - i가 2일 때, 
        - Need : 1, 0, 0
        - Work : 5, 3, 2
3. work = work + allocation
4. work가 다 finish면 그것이 safe alforithm
### Resource-Request Algorithm