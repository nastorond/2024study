# 데드락(교착상태)의 이해
가장어려운 파트...

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
- E : edges  